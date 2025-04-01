+++
title = 'Loki with Extstore'
date = 2024-09-04T14:58:37-07:00
+++

## Grafana Loki with Extstore

You may have seen this excellent blog post: https://grafana.com/blog/2023/08/23/how-we-scaled-grafana-cloud-logs-memcached-cluster-to-50tb-and-improved-reliability/

... and are now attempting to make use of this knowledge, but something isn't
working quite right. This document will give you a quick start in tuning Loki
and Extstore to work well together.

## TLDR

We assume your Loki chunk storage size is 1.5mb

For memcached, add at least the following tuning options:

`-I 2m -o ext_wbuf_size=32,ext_threads=10,ext_max_sleep=10000,slab_automove_freeratio=0.10,ext_recache_rate=0`

IE, your full start line may look like:

`memcached -m 6000 -I 2m -o ext_path=/disk/extstore:500G,ext_wbuf_size=32,ext_threads=10,ext_max_sleep=10000,slab_automove_freeratio=0.10,ext_recache_rate=0`

*Please set -m and ext_path appropraitely for your system*. Leave some RAM for
your system to breathe and a little disk space overhead.

Please use version 1.6.21 or newer as it improves the extstore write speed and
fixes some related bugs.

In your loki configuration:

```
chunk_store_config:
  chunk_cache_config:
    memcached:
      batch_size: 3
      parallelism: 2
    memcached_client:
      addresses: 127.0.0.1:11211
      timeout: 60s
    background:
      writeback_goroutines: 1
      writeback_buffer: 1000
      writeback_size_limit: 500MB
```

NOTE: `batch_size` can be set to 2x the number of memcached servers you have.
So if you have 3 servers, 6 should work. Keep `parallelism` as low as
possible, but increase this value if you are not maxing out network usage on
memcached.

Loki's default configuration is very aggressive, which is normally fine for
memory backed memcached. However extstore needs a little more time to fetch or
write to disk.

Finally, please check that your memcached instances and loki instances aren't
swapping (out of RAM) or out of CPU, as this can make query times longer and
cause timeouts.

---

### Why we have to tune these settings

Loki's defaults assume both A) A RAM backed cluster, and B) potentially a
_large_ cluster made of tens to dozens to hundreds of cache nodes. Many users
are trying a low number of memcached nodes with extstore (1-3).

When Loki fetches keys from a _pool_ of memcached servers, it will fetch a 
`batch_size` of keys to the _entire pool_ all at once. If you have a
`batch_size` of 500 and 40 memcached servers, _each memcached_ will receive
12-14 keys at the same time, as the batch is split across them.

If you are fetching `500` keys against `1` server, that is a much larger batch
of keys hitting a single server. There are other issues with this but this
document will not discuss them for now.

### Memcached tuning discussion

The defaults for extstore are fairly conservative. Most of the performance
improvement you will see is from raising `ext_threads`, which allows it to
fully utilize an SSD.

If you have a particularly fast SSD, the thread count can be further raised to
20 or 30.

The number of memcached worker threads is specified with `-t` and defaults to
`4`, do _not_ set this higher than the number of CPU's your server has.

The rest of the tunings help provide minor speedups.

### Loki tuning discussion

Above we discuss the `batch_size` problem. You can also see `timeout` is set
very high. We run into issues for a few reasons:

- Loki's memcache client timeout is measuring the amount of time to _fetch and
  read and process the entire batch of keys from each host_.

If you are fetching 2 keys from one host (3MB of data), the 100ms default might seem okay. However, retrieving 500 1.5 megabyte keys over the network from SSD on one host might take quite a while. If you have fewer memcached hosts, or your Loki server does not have a lot of CPU to process results quickly, this timeout will need to be set very high.

We will update this document if this changes.

- `writeback_goroutines: 1`

This defaults to 10. Loki will aggressively write all of the data it fetches
from a backing store _back to memcached_ as each query runs. Memcached keeps
some memory reserved as a buffer to give it time to flush data to disk. If it
cannot write to disk fast enough, you will see the `evictions` counter
increase.

There are not a lot of good options at the moment, but setting this value to
`1` will help minimize the impact. If you still have trouble with `evictions`
you may need to scale up to a faster memcached instance or add more instances.

We will update this document if anything changes.
