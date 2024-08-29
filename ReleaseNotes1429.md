### Memcached 1.4.29 Release Notes

Date: 2016-7-13

### Download

http://www.memcached.org/files/memcached-1.4.29.tar.gz

### Overview

Improved support for large items over the 1 megabyte default. Also includes a
handful of features.

Large items now have much better memory efficiency, and can get very large
(potentially hundreds of megabytes) safely.

To make use of the new feature, simply use the `-I` option as before, IE:

`-I 2m`

Many thanks to https://www.packet.net/ for providing high speed baremetal
hardware which was used for performance and burn-in testing of this new
feature.

### Fixes

  * fix items with a "0" hash result from failing to evict sometimes.
  * allow cachedump'ing of slab 63.
  * fix flakiness of slabs-reassign2.t test.
  * Fix ancient binprot bug with sets resulting in OOM errors desycning the
    protocol.

### New Features

After over a decade of having an item size target of a single megabyte, items 
can now safely raise above that. Up to hundreds of megabytes, potentially.

This feature automatically enables if you raise the item size limit above 1mb.
IE: `-I 2m`.

You can also enable it manually with a limit of 1mb (the default of `-I 1m`) or
less (like `-I 512k`) by setting `-o slab_chunk_max=16384`
(16384 is the recommended default).

Full details in the pull request:
https://github.com/memcached/memcached/pull/181

In summary:

 * Memcached uses a slab allocator, which splits up memory into chunks of
   specific sizes.
 * The "item size limit" has always been the largest possible slab chunk.
 * Increasing the size limit thus spreads out available slab classes, reducing
   memory efficiency.

Now:

 * The maximum slab chunk size is no longer tied to the maximum item size.
 * Items larger than the slab chunk size now comprise of multiple chunks of
   the largest size.

Benefits:

 * With a smaller maximum chunk size, the slab classes for small items are now
   more fine grained raising memory efficiency.
 * Items that approach 1mb can waste more memory as the classes are far apart.
   Chaining items together can help a lot here.
 * Performance should be good, as small items are still 1:1 and large items
   soak more bandwidth, allowing extra room for a very small amount of CPU
   overhead.

Current caveats:

 * The system has always been able to store an item after evicting exactly one
   item. This is no longer the case for large items. If you mix very large
   items (say 5 megabyte), with many small-ish items (say 100k), with a slab
   chunk max of 16k, memcached may return more OOM errors than normal while
   attempting to free enough space to store a new 5 megabyte item.
 * With a slab chunk max of 16k, an item that requires 16k + 1 byte of memory
   will use 32k of memory. The larger the item the less effective overhead
   this has, but at the smaller levels it can lead to problems. Simply leave
   settings at their defaults or raise the `slab_chunk_max` if you run into this.
 * There be dragons if you attempt to store multiple gigabyte items. The item
   size attributes are 32bit.

These issues should improve in future releases after some refactoring.
IE: It should be possible to comprise a chunked item of more than one chunk
size, alleviating the last-chunk inefficiency.

To get the most out of this feature, we again recommend you start memcached
with `-o modern`, or many of its new defaults. This allows it to reassign
memory as necessary and improves the LRU algorithm significantly.

### Contributors

The following people contributed to this release since 1.4.28.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.28..1.4.29` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.29

```
    17	dormando
     1	David Oliveira
     1	Eiichi Tsukata

```
