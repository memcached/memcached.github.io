+++
title = 'Flash Storage'
date = 2024-09-02T11:29:54-07:00
+++

This feature is for extending memcached's memory space onto flash (or similar) storage.

Requires memcached 1.6.0 or newer.

## Quick Start

Extstore is an addition to memcached which leaves the hash table and keys in
memory, but moves values to external storage (usually flash).

### When to use this?

If you only have one or two memcached instances, you probably don't need this.

If stored values are much larger than keys and your hit rate would improve
if you stored data for longer. You may save on cost (RAM can be expensive in
"the cloud") if you have network capacity so spare.

There are useful tools online for analyzing memcached's stats
output, but you can also simply telnet in and run `stats` and `stats slabs`
and read the output. If you slab classes with large `chunk_size` values also
have a relatively high number of  `total_pages`, and you see evictions, you
can give this a try.

You should also have flash storage. This will not work with rotational media.

### How to use it?

Grab the latest memcached.

Build with: `./configure && make && make test && sudo make install`

Use your normal startup options (-m -c, etc), and add:

`-o ext_path=/path/to/a/datafile:5G`

This initializes extstore with up to 5 gigabytes of storage. Storage is split
internally into pages. By default pages have a size of 64 megabytes.

### How to tune it?

Unless your servers are very busy, shouldn't do any further tuning. Try to monitor
the output of `stats` and how much is being written to disk.

If you write too much to memcached, you may write too much to flash, which can
burn out the drive or cause too much latency!

See below for a full description of tuning options.

---

# Tuning guide

Before tuning, you should have a good grasp of your operating system, how to
monitor latency, disk utilization, CPU utilization, etc. You should have a
graphing system setup to monitor trends over time, including memcached
statistics. You should be able to monitor your hit ratio, and graph various
`extstore_` stats via the `stats` command.

### Why tune?

If you have a high capacity system (hundreds of gigs to terabytes),
pushing hundreds of megabits to gigabits, you may see
poor latency or poor hit rate. You may also see excessive writes to disk,
which can age or burn out the device. Most server-quality flash devices are
rated to be overwritten only 1-5+ times per day.

### Primary tradeoffs

Items are flushed to flash storage from the tail of the LRU's, which are
typically the least active items. The key and item structure (timestamps,
flags, etc) plus 12 bytes for the flash location are kept in RAM. These are
stored the same as normal items.

This means an item in slab class 50 using a hundred kilobytes of RAM would
be flushed and reappear anywhere in slab class 1-6, depending on how long the
key was.

If your values are only 60 bytes, you won't be saving much RAM (if any!).

Items are first written to a "write buffer" in RAM, which then gets flushed to a
page. Once a page is full, it's capped and another one is then used.

Since items are pulled from the LRU tail, you are naturally less likely to hit
the flash to retrieve them again later.

If you're writing new data to memcached faster than it can flush to flash, it
will *evict* from the LRU tail rather than wait for the flash storage to
flush. This is to prevent many failure scenarios as flash drives are prone to
momentary hangs.

- extstore is explicitly not a levelled storage system, which is often used to
  manage write loads. In order to support a cache workload on flash media,
more data needs to be kept in RAM, and accesses to flash need to be direct to
minimize latency.

### Performance expectations

- Many operations don't touch flash. IE: `touch`, `delete`, overwriting a
  value already on flash (including `add`), TTL expiration. They should be the
same speed as without extstore.

- Only reading items that were flushed, writing items from the end of the LRU,
  and compacting existing flushed items, touch storage.

- In most deployments, items toward the end of the LRU are infrequently hit,
  but this can still be helpful if reducing misses has a large impact on hit
ratio.

- You may only want to flush very large items (64k+), which frees up the
  most RAM, and are very easy for a flash device to saturate a network with.
The smaller the items stored on flash, the more likely you are to saturate the
flash device before your network device.

### What to watch for

- `evictions` can mean you're out of RAM or you're writing too fast and extstore
  cannot keep up.

- very low `age` (via `stats items`) in higher slab classes, may mean too much
  memory is in use for small items. You can use less flash storage, shorten
keys, or add RAM to the server.

- too much writing to disk. disk latency spikes. disk hangs. there aren't any
  internal measurements of disk latency as of this writing.

- too much writing can be alleviated by adding more RAM and tuning compaction
  settings as per below. Recently set objects are the most likely to be
re-written or deleted, so the longer they can be kept in RAM the less writing
you end up doing on flash.

### Page buckets

Storage "pages" are split into different "buckets", which allows extstore to
colocate items. Currently there are buckets for:

- Default, all non-special items end up here.

- Large items (> 512k, or larger if the item size max is increased).

- Compacted items (rewritten items are stored together to reduce fragmentation)

- "low" ttl items. This bucket isn't used by default. Pages used by the low
  bucket are ignored by compaction, which can reduce writes to disk. By
storing these separately from objects with a longer TTL, fragmentation can be
reduced significantly.

### Tuning options

(see --help or `stats settings` for the latest defaults. Version 1.5.4 may not
contain all defaults in these outputs yet.

- `ext_page_size` defaults to 64M. This shouldn't need to change unless you
  are storing very large values (multiple megabytes).

- `ext_wbuf_size` defaults to 8M. One write buffer is needed per bucket, By
  default 32 megabytes of memory are reserved for write buffers. This cannot
be larger than the page size. Larger write buffers can help with write rate
performance.

- `ext_threads` defaults to 1. If you have a high read latency but the drive
  is idle, you can increase this number. Stick to low values; no more than 8
threads.

- `ext_item_size` defaults to 512 bytes. Items larger than this can be flushed.
You can lower this value if you want to save a little
extra RAM and your keys are short. You can also raise this value if you only
wish to flush very large objects, which is a good place to start.

- `ext_low_ttl` TTL's lower than this value will be colocated on disk. Off by
  default. Useful if you have a mix of high and low TTL's being used.
Extstore has to compact or outright evict pages once flash is full. Mixing
high and low TTL objects in a page will mean a page gets partially empty after
some amount of time.

For example, if you only store objects with TTL's of 86400 (about a day) or
2400s. If you set `ext_low_ttl` to 2400 or higher, all of the latter objects
will get stuck together. Once all of those objets have expired, the page they
were in is reclaimed entirely. This avoids holes and compaction necessary if
placed alongside the longer TTL'ed objects.

This option may change in the future; even more buckets may be useful.

- `ext_max_frag` A compaction tuning flag. If there are few free pages
  remaining, pages which are at least this empty will be rewritten to reclaim
space. IE: `ext_max_frag=0.5` will rewrite pages which are at least half
empty. If no pages are half empty, the oldest page will be evicted.

- `ext_drop_unread` A compaction tuning flag. If enabled, and no pages match
  the `ext_max_frag` filter, a compaction will be done anyway. However, only
items which are new or frequently accessed (ie; not in the COLD LRU) will be
rewritten. This can cause more writing than simply evicting, but will keep
active items around and raise hit ratio.

- `ext_compact_under` A compaction tuning setting. If fewer than this amount
  of pages are free, check the `ext_max_frag` filter and start compacting.
Defaults to 1/4th of the pages.

- `ext_drop_under` A compaction tuning setting. If `ext_drop_unread` is
  enabled, and fewer than this many pages are free, compact and drop COLD
data. This defaults to the same value as `ext_compact_under`.

- `ext_recache_rate` If an item stored on flash has been accessed more than
  once in the last minute, it has a one in N chance of being recached into RAM
and removed from flash. Defaults to 2000. It's good to keep this value high;
recaches into RAM cause fragmentation on disk, and it's rare for objects in
flash to become frequently accessed. If they do, they will eventually be
recached.

### Runtime tunables

Many of the values above can be adjusted at runtime.

Via a socket to memcached: `extstore [command] [optional value]`. As of this
writing, commands are:

`item_size item_age low_ttl recache_rate compact_under drop_under max_frag
drop_unread`

The effects are the same as documented above.

---

# Future plans

`TODO`: Some info in the rough notes below, as well as doc/storage.txt.

# Rough notes

See doc/storage.txt for API and some implementation details.
See `memcached -h` for start arguments.

Primary features:
 - very high speed. minimal CPU overhead.
 - writes are buffered and flushed sequentially. very good for improving read workload on most devices.
 - keys and headers are left in memory, while data is written to disk. (see below)
 - page compaction for reducing fragmentation over time.
 - multikey reads can be batched to reduce latency from multiple swaps to IO threads.
 - simple. uses a basic thread pool for IO's, suspends/resumes connections.
 - does not block if items are being SET faster than they can be flushed to flash. objects are instead evicted from tail of memory to make space. (reliable, predictable speed)
 - live control of what gets flushed to flash, ie: "objects >= 1024 bytes which are 3600s idle"

Tradeoffs:
 - All data is tracked in memory. A restart of memcached effectively empties flash.
 - Objects leave a small part in memory + the original key. This means very small objects don't get any benefit from being flushed.
 - UDP is not be supported for objects flushed to disk.
 - Not a levelled (or any kind of sophisticated) database. It wants to be fast and forget things, rather than efficiently use 100% of disk space. Expect 80-90% best case.

## Very high level detail

A single file is split into N logical pages of Y size (64M default). Write buffers (8M+) are used to store objects later flushed into pages.

IO threads are used to asynchronously flush write buffers and read items back.

Items are left in the hash table along with their keys, with small (12 byte as of this writing) headers detailing where data lives in flash.

The storage engine recycles pages by changing their version number. Item headers remember both the page ID and version number at the time the object was flushed. An attempted read with the wrong version will result in a miss.

The system is most useful if you have a breakdown of different item sizes: pools with a mix of small and large, or if you can break off a small pool of machines with flash devices to store much larger items. Freeing up more memory on your small item pools can dramatically improve hitrates, or simply reduce costs overall.

## Performance

This is designed for maximum performance. It unifies into the existing hash table data structure in order to avoid burning CPU from shifting items into a secondary system. This also greatly reduces the IOPS required to maintain data.

IE: Rewriting an object does not touch flash. It simply "forgets" the position of the previous item on disk and informs the engine that particular page has fewer bytes stored. The same exists for deletes.

Recycling pages also does not require writing to them.

Compaction however does require rewriting pages. More details of that is in doc/storage.txt

## How do I use it?

`-o ext_path=/data/file:5G` this is enough to get you started. See `memcached --help` for other options. This wiki will update with more details on what the options do, but most users should be able to use a minimal subset of options. If you have a very high performance setup, feel free to reach out and we can walk you through a configuration.

The above creates an external cache of 5 gigabytes. Due to how the shim works, you wil not be able to actually store 5 gigabytes due to fragmentation over time. Background jobs will attempt to keep fragmentation in check.

## How safe is this really?

I test pretty aggressively while developing features. The general stability of memcached should attest to a high quality. This feature is still in an experimental tages, but has had a good amount of baking time for the operations it does support. Keep in mind that many features are disabled for items sent to disk (incr/decr/append/prepend)

---

# Technical Breakdown

This section is a detail on the architecture and tradeoffs of external
storage.

### Goals

Primarily: keeping "cache patterns" in mind, get the most out of a storage device by
 minimizing the accesses to it.

* Larger values (typically 1k+) can use flash. Small values stay in RAM.
* A single read to serve a single key.
* Asynchronous batched writes to drive.
* Main hash table is authoritative: miss/delete/overwrite must not use the drive.
* Writing new items to cache must never block on the flash device.
  * Items are evicted from the LRU tail in RAM if extstore writer lags.
* Forgetting / evicting data from storage must not use the drive.

### Assumptions

Some assumptions about access patterns have to be made in order to utilize
flash as a cache medium. Writing is expensive (destructive to the drive), and
caches typically have much higher churn rate than databases. Flash also
increases the latency of each key fetched, which can make caching small values
on flash ineffective compared a range fetch against a high end database.

* Longer TTL's for data that hits disk (and good reuse)
* Latency overhead for small items is high compared to benefit of storing
  them.
* Latency overhead for larger items is better than a miss (within reason)
* A minority of data is hot (highly accessed), with a long tail of colder
  data.

### Architecture

A review of the main threads and data structures in memcached:
```
               THREADS            +            STRUCTURES
 +----------------------+         |
 |-----------|          |         |     +----------+
+------+  +--v-----+ +--v-----+   |     |hash table|
|listen|  |worker 1| |worker 2|   |     +----------+
+------+  +--------+ +--------+   |
                        +--------->     +---+
                                  |     |LRU|
+--------------+                  |     +---+
|lru maintainer+------------------>
+--------------+                  |     +--------------+
                                  |     |slab allocator|
+-----------+                     |     +--------------+
|lru crawler+--------------------->
+-----------+                     |
                                  |
+---------------+                 |
|slab page mover+----------------->
+---------------+                 |
                                  +
```

_THREADS_

The listen thread accepts new connections, it then passes the socket to
workers via writing a byte to a pipe. Each worker has its own "notifier
pipe", which it receives events from via libevent polling. This mechanism is
reused for extstore, allowing IO requests to pass to/from IO threads.

A client connection sticks to a worker thread for its lifetime. One worker
thread per CPU is ideal.

The LRU maintainer thread moves items inbetween sub-LRU's (hot|warm|cold|etc).
It also makes decisions on when to schedule page moves, or LRU crawls.

The LRU crawler reaps expired/invalid items, including items extstore
invalidates.

Memory is divided into slab classes with fixed chunk sizes. Fixed size (1M) pages
are assigned to each slab-class and divided into chunks as-needed. Over time,
shifts in average item sizes or access patterns can "starve" a class. The
page mover thread is able to safely re-assign pages between slab classes to
avoid starvation.

Misc. threads: hash-table-expander, logger. These do not interact with extstore.

_STRUCTURES_

* Hash table is chain bucketed. "next" is embedded in item memory.
* LRU is doubly-linked, with next/prev embedded in item memory.
* Each slab class has its own LRU.
* Each LRU is split into HOT|WARM|COLD|TEMP

---

Now, with extstore:
```
               THREADS            +            STRUCTURES
 +----------------------+         |
 |-----------|          |         |     +----------+
+------+  +--v-----+ +--v-----+   |     |hash table|
|listen|  |worker 1| |worker 2|   |     +----------+
+------+  +--------+ +--------+   |
                  |     +---------+     +---+
                  |               |     |LRU|
                  |               |     +---+
                  |               |
+-------+       +-v--+   +----+   |     +--------------+
|storage+------->IO 1|   |IO 2|   |     |slab allocator|
+-------+       +----+   +-^--+   |     +--------------+
                           |      |
+----------+               |      |     +--------------------+
|compaction+---------------+      |     |extstore page memory|
+----------+                      |     +--------------------+
                                  |
                                  |     +----------------------+
                                  +     |extstore write buffers|
                                        +----------------------+
```

_THREADS_

The storage thread is similar to the LRU maintainer (they may merge later). It
examines the LRU tails for each slab class. It requests a write buffer from
extstore, CRC32's the item data, then hands the write buffer back to extstore.

The compaction thread targets pages ready for compaction. It reads the page
data back from storage, walks the items within, and either throws them away or
rewrites into a new page. See below for more detail.

Multiple IO threads exist which issue pread/pwrite/etc calls to the underlying
storage on behalf of worker, storage, and compaction threads. Each IO thread
has a simple stack queue with a mutex.

There is also an "extstore maintenance thread", which simply sorts page data
for other threads. It may disappear and become an API call for the LRU
maintainer.

_STRUCTURES_

Extstore has some of its own data structures. Mostly stats counters, and page
data.

Pages on disk are (default 64M) subsections of a file or device. Each page has
a small structure in memory. The structure in memory is used to avoid writing
to flash when objects are deleted from a page. A page can be reclaimed once
fully empty, so a page can be recycled without reading or writing back to it.

Extstore has write buffers which items are written into from the storage or
compaction threads. When write buffers are full, a write will fail temporarily
while the buffer is send to an IO thread. Once flushed, writes are accepted
into the buffer again. This is used to rate limit writes to storage.

---

#### How an item flows to storage:
```
+--------+
|LRU tail|
+--------+
  |
  | * Is the item large?               +------>
  |                                    |
  | * Is memory low?                   | Once written to buffer:
  |                                    |
  | * Is a write buffer available?     | * Allocate new small item
  |                                    |
  |   +------------+                   | * Copy key+metadata to new item
  +--->write buffer+-------------------+ * Also contains pointer to flash:
      +------------+                       [page, offset, version]

    * IF the write buffer was full:      * Replace original with small item

     +------------+     +---------+
     |write buffer+----->IO thread|
     +------------+     +---------+

    * All writes wait during flush
```

* If all memory is exhausted while writes to storage are waiting, items will
  be evicted from the LRU tail in memory. IE: An item in slab class 10 waiting
  to be written to storage, will instead be evicted. Sets to cache must not
  block on storage.

---

#### How an item is read from storage:
```
   +-------+
+--+request|
|  +-------+
|
|  +-----------------+
+-->hash table lookup|
   +-----------------+

    If item is a flash header:

    * Create IO request for extstore

    * Continue parsing other keys in request

    Before response is written to the client,
    if it contains IO objects:

 +--------------------------------------------+
1                     4
   +-------------+     +-------------+
+--+worker thread<-----+libevent poll<----+
|  +-------------+     +-------------+    |
|                                         |
|  +---------+       +------------------+ |
+-->IO thread+------->worker notify pipe+-+
   +---------+       +------------------+
2                   3
```

* With multigets, many IO objects can be sent to the IO thread at the same
  time. Typical flash devices are internally parallel, and can have similar
  latency for batched requests.

* Workers fill iovec's with response data while parsing each key in the
  request. For every item header, blank iovec's are added where the data would
  be.

* A temporary item is allocated from slab memory to hold the full item.

* If an item has become invalid (page evicted, etc), the IO thread will record
  a miss back into the IO object before shipping back to the worker.

* On a hit, the holes are filled in the iovec with the temporary item.

* On a miss, the affected iovec entries are are NULL'ed out, wiping the
  response.

* Temporary memory is freed at the end of the request.

Items read from storage have a chance of being recached into memory if they
are read multiple times in less than a minute. This allows items which are
only occasionally accessed to stay on storage. When they are recached into
main memory, their flash entry is deleted.

---

#### The lifetime of a storage page:
```
[/data/extstore]

OFFSET + ID  + VERSION
       |     |
 0M    | P0  | 5
 64M   | P1  | 10
 128M  | P2  | 12
 192M  | P3  | 6
 256M  | P4  | 7
 320M  | P5  | 8
 384M  | P6  | 9
       |     |
       |     |
       |     |
       +     +
```

Storage pages are offsets into a file, with a default size of 64 megabytes per
page. Future releases should allow multiple files (thus devices) be used for
the same page pool. As of this writing, only buffered IO is used. O_DIRECT and
async IO's are planned.

Each page has a version assigned to it as it is used. When a page is recycled
or evicted, it is given a new version number.

Item headers in memory store: [PAGE ID, OFFSET IN PAGE, VERSION NUMBER]. The
version number is used to validate an object is still valid when being fetched
from storage.

Pages are organized into logical "buckets". In the extstore shim, buckets are
arbitrary numbers. Memcached's higher levels give the buckets meaning

```
OPEN      [BUCKETS]     FULL

          +-------+
[P9]<-----+DEFAULT+---->[P0,P1,P2,P5,P6]
          +-------+

          +---------+
[P10]<----+COMPACTED+-->[P3,P4]
          +---------+

          +-------+
[P11]<----+LOW TTL+---->[P7,P8]
          +-------+


FREE: [P12,P13,...]
```

Buckets are used to coalesce data into related pages. By default all items are
written into bucket 0 [DEFAULT].

* If an item has been rewritten during compaction (see below), it is written 
  into [COMPACTED] which are reserved for items having survived compaction.

* If an item has a low remaining TTL at flush time (see `ext_low_ttl` option),
  it will be written into the [LOW TTL] bucket. This bucket is excluded from
  compaction. Pages are reclaimed periodically as the items expire.

[COMPACTED] pages tend to contain very long lived objects, so those pages have
less fragmentation and need to be rewritten less often.

[LOW TTL] pages are never compacted, which avoids write amplification at the
cost of some space over time.

In the future, [LOW TTL] may split into multiple buckets. Splitting IE: low,
med, high, would allow very low pages to reclaim faster while still avoiding
write amplification for higher TTL's.

---

#### How deletes, and overwrites work:
```
+--------+         +----+
|ITEM HDR+----+----+PAGE|
+--------+    |    +----+
              |
[DELETE]      | [1048576 bytes stored]
              | [6000 objects stored]
* Remove from |
  hash table  | ---
              |
              | [1048401 bytes stored]
              | [5999 objects stored]
              |
[REPLACE]     |
              | ---
* Insert into |
  hash table  | No change
              |
              +
```

Pages track a few counters in memory only. The number of bytes stored, and the
number of objects stored. A page has no way of retrieving what items are
actually stored within it.

Deleting an object from a page reduces the object and byte count from the
in-memory counters associated to that page. The same goes when page are being
filled in the first place, the counters are incremented.

* When bytes and objects in a page reach zero, it is immediately reaped and
  placed onto the free list.

* Deletes or overwrites never cause new writes to happen to
  storage.

---

#### The compaction thread:

* First, find a candidate to compact. IE: page is more than 50% empty. (tuned
  by `ext_max_frag`).

* Since pages are written to aligned by write buffer size, the thread reads
  data back from the page in chunks the size of the write buffer (IE: 8M).

* The buffer is walked linearly. Since items are fully written to storage,
  the read buffer can be parsed as item structures. Once an item is examined,
  the next one is found by advancing a pointer by item size.

* When an item is examined:
  * The key hash is retrieved from storage pre-calculated, which speeds up
    readback.
  * An item lock is taken, and the hash table is checked for the original
    item.
  * If the item exists, and is still an item header which points into the same
    page at the same version, it's queued to be rewritten.
  * IF `ext_drop_unread` is enabled, a valid item is only rescued if it was
    recently accessed (specifically; if it's not in the COLD LRU).

drop_unread is important if a lot of data is being written through storage.
This ensures objects which are getting requested, but not frequently enough to
be recached into memory, aren't lost.

---

#### Memory thresholds:

The slab page balancer has an algorithm specific for extstore configurations.

For reference, [a python](https://github.com/memcached/memcached/blob/master/scripts/memcached-automove-extstore) and [internal C version](https://github.com/memcached/memcached/blob/master/slab_automove_extstore.c) of the algorithm exist.

When tuning changes to the algoritm, it's easy to disable the internal
algorithm and use external scripts.

Since the extstore system is designed to send items to storage before they're
removed from memory, it needs to manage a buffer of free space. 1% or 0.5%.

* In each slab class, 0.5% of chunks assigned to the class "should be free"
* Overall, 0.5% of (1M) memory pages should be kept in the global page pool,
  to be assigned as needed.

These targets allow the storage thread some time to react to bursts of traffic
without having to drop memory. If free memory drops below the threshold, the
storage thread works to catch up.

* Item headers use memory out of the global page pool, which ensures memory is
  available as items are pushed to storage.
* If the global pool gets low, pages are reclaimed from the larger slab
  classes.

_NOTE_: The algorithm is currently does not prevent small items from taking
all memory. If items are relatively small, or a very large amount of storage
space is available compared to RAM, there will be no memory to hold other
items. This will cause the flash to be hit much harder as very recent items
are written out.

Caps may be introduced in the future.

---

### Tradeoffs

[TODO]
* Read amp.
* Full readback during compaction.
* Write amp.
* No small items.
