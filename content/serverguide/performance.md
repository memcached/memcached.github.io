+++
title = 'Performance and Efficiency'
date = 2024-09-04T13:42:02-07:00
prev = '/serverguide/maintenance/'
next = '/serverguide/hardware/'
weight = 3
+++

We expect memcached to be fast. Occasionally folks test memcached and see something they don't expect. This is a short list of how we (the developers and users) expect memcached to perform. If you're seeing different, it's likely a configuration problem at some layer. You should troubleshoot or request help in getting a properly oiled setup.

## General Performance
----

### Handles Extremely High Load

On a fast machine with very high speed networking, memcached can easily handle 200,000+ requests per second. With heavy tuning or even faster hardware it can go many times that. Hitting it a few hundred times per second, even on a slow machine, usually isn't cause for concern.

### It Should Not Hang

Memcached operations are almost all O(1). Connecting to it and issuing a get or stat command should never lag. If connecting lags, you may be hitting the max connections limit.

If issuing commands lags, you can have a number of tuning problems. Most common are hardware problems, not enough RAM (swapping), network problems (bandwidth, dropped packets, half-duplex connections). On rare occasion OS bugs or memcached bugs can contribute.

#### OS Issues / Firewalls

It's not usually necessary to run a stateful firewalling system in front of a memcached server. You might if you run them by default, or need to protect memcached from the internet at large.

These systems have limits in the number of connections that can pass through it in a certain number of time, and how fast those connections or packets can pass through. A lot of connectivity bugs end up being tracked back to someone's firewall.

### It Should Respond Quickly

On a good day memcached can serve requests in less than a millisecond. After accounting for outliers due to OS jitter, CPU scheduling, or network jitter, very few commands should take more than a millisecond or two to complete.

### Expiration Times Should Be Accurate

Memcached tries to expire items when you tell it to. To avoid hitting the clock constantly, it updates an internal clock value every second. This means your expiration times should be accurate up to the nearest second. So an expiration can be a second early or a second late, but should not waver beyond that.

Since expiration values are stored as timestamps, be sure your OS clock is correct. If your OS clock is set to some time in the future, you're storing items into memcached, and you suddenly adjust the clock far into the past, those items will not expire on time. Inversely, jumping a clock forward can prematurely expire items.

### How It Handles `set` Failures

There are conditions which memcached will "fail" a set. If it cannot find free memory, if the object is too large, or some unknown factor. In most cases where you are issuing a `set` against an item that already exists, a failure will cause the old item to be removed from the cache.

Memcached assumes that if you are issuing a set, even if the item is invalid, that means your intent was to update the cache, and whatever it contains is now stale. It error's on the side of caution and throws the old item out for you.

## Theoretical Limits
----

### Max Clients

Since memcached uses an event based architecture, a high number of clients
will not generally slow it down. Users have hundreds of thousands of connected
clients, working just fine.

There are a few hard limits:

  * Each connected client uses some TCP memory. You can only connect as many clients as you have spare RAM

   * There is only one thread to accept new client connections. If you are cycling connections very quickly, you can overwhelm the thread. Use persistent connections or UDP in this case.

  * High connection churn requires OS tuning. You will run out of local ports, TIME_WAIT buckets, and similar. Do research on how to properly tune the TCP stack for your OS.

### Maximum number of nodes in a cluster

#### From the Client Perspective

A large number of servers can slow down a client from a few angles. If you are
calculating the server hash table on every request, that will slow down as
you add servers. Most clients will calculate the table once on startup and
reuse it between requests.

Looking up a server to issue a request is simply a hash lookup, so it is not a
performance issue.

  * Establishing too many TCP sockets from the client wastes RAM

  * Disabling persistent connections means the client will likely connect to all servers on every request. The 3 way handshake will add latency and packets to each request.

#### The Multiget Hole

There was once a discussion on a facebook note noting that as you add servers
to a cluster, the more spread out multigets are. If you issue a multiget
request for 10 keys against a 2 server cluster, that will turn into two
syscalls; one for each server. If you have 10 servers, you will end up with
one syscall per server. The servers themselves are doing more syscalls to cope
with the smaller incoming requests, and their capacity drops.

This is not an insurmountable problem. It's possible with many clients to
group related keys together. IE; all keys for a particular user's profile
would end up on the same memcached instance, so a multiget will hit a single
server with all of its keys and use far fewer syscalls in total.

#### A Well Designed Binary Protocol Client

Using spymemcached as an example; the client may take many application threads
and use a single TCP connection back to memcached. With the binary protocol,
it is possible to pack requests from different client instances into the same
TCP socket, then dole back results to the right owners.

This means an application server, could in theory only require a single TCP
socket per memcached instance. If you run 50 threads or processes per
application node, this can vastly reduce overhead from large installs.

#### Remember Moore's Law

If many large sites were using the same hardware they had five years ago for new
memcached instances, they could have four times or more the server count than
they do now. As years pass memory becomes cheaper and network capacity
increases. Hardware increases won't necessarily stay behind your growth curve,
but they do take a huge edge off as you can in theory halve your server count
every few years.

## How Memory Gets Allocated For Items

Memory assigned via the `-m` commandline argument to memcached is reserved for item data storage. The primary storage is broken up (by default) into 1 megabyte pages. Each `page` is then assigned into `slab classes` as necessary, then cut into chunks of a specific size for that `slab class`.

Once a page is assigned to a class, it is *never* moved. If your access patterns end up putting 80% of your pages in class 3, there will be less memory available for class 4. The best way to think about this is that memcached is actually many smaller individaul caches. Each class has its own set of statistical counters, and its own LRU.

Classes, sizes, and chunks are shown best by starting up memcached with `-vv`:

```
$ ./memcached -vv
slab class   1: chunk size        80 perslab   13107
slab class   2: chunk size       104 perslab   10082
slab class   3: chunk size       136 perslab    7710
slab class   4: chunk size       176 perslab    5957
slab class   5: chunk size       224 perslab    4681
slab class   6: chunk size       280 perslab    3744
slab class   7: chunk size       352 perslab    2978
slab class   8: chunk size       440 perslab    2383
slab class   9: chunk size       552 perslab    1899
slab class  10: chunk size       696 perslab    1506
[...etc...]
```

In slab class 1, each chunk is 80 bytes, and each page can then contain 13,107 chunks (or items). This continues all the way up to 1 megabyte.

When storing items, they are pushed into the slab class of the nearest fit. If your key + misc data + value is 50 bytes total, it will go into class 1, with an overhead loss of 30 bytes. If your data is 90 bytes total, it will go into class2, with an overhead of 14 bytes.

You can adjust the slab classes with `-f` and inspect them in various ways, but those're more advanced topics for when you need them. It's best to be aware of the basics because they can bite you.

### What Other Memory Is Used

Memcached uses chunks of memory for other functions as well. There is overhead in the hash table it uses to look up your items through. Each connection uses a few small buffers as well. This shouldn't add up to more than a few % extra memory over your specified `-m` limit, but keep in mind that it's there.

### When Memory Is Reclaimed

In versions prior to 1.5.0, by default expired items are not actively
reclaimed. If running old versions with `-o modern` start option, or post
1.5.0, a background thread periodically scans the cache and frees expired objects.

Also, if you fetch an expired item, memcached will find the item, notice that it's expired, and free its memory.

Items can also be evicted to make way for new items that need to be stored, or expired items are discovered and their memory reused.

### How Much Memory Will an Item Use

An item will use space for the full length of its key, the internal datastructure for an item, and the length of the data.

You can discover how large an Item is by compiling memcached on your system, then running the "./sizes" utility which is built. 

Example:
```
$ ./sizes 
Slab Stats	56
Thread stats	176
Global stats	108
Settings	88
Item (no cas)	32
Item (cas)	40
Libevent thread	96
Connection	320
----------------------------------------
libevent thread cumulative	11472
Thread stats cumulative		11376
```

### When Are Items Evicted

Items are evicted if they have not expired (an expiration time of 0 or some time in the future), the slab class is completely out of free chunks, and there are no free pages to assign to a slab class.

#### How the LRU Decides What to Evict

Memory is also reclaimed when it's time to store a new item. If there are no free chunks, and no free pages in the appropriate slab class, memcached will look at the end of the LRU for an item to "reclaim". It will search the last few items in the tail for one which has already been expired, and is thus free for reuse. If it cannot find an expired item however, it will "evict" one which has not yet expired. This is then noted in several statistical counters.

See [LRU Documentation](https://github.com/memcached/memcached/blob/master/doc/new_lru.txt) for detail on the latest LRU algorithm.

### libevent + Socket Scalability

Memcached uses [libevent](http://www.monkey.org/~provos/libevent/) for scalable sockets, allowing it to easily handle tens of thousands of connections. Each worker thread on memcached runs its own event loop and handles its own clients. They share the cache via some centralized locks, and spread out protocol processing.

This scales very well. Some issues may be seen with extremely high loads (200,00+ operations per second), but if you hit any limits please let us know, as they're usually solvable :)

