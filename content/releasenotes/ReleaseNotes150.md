### Memcached 1.5.0 Release Notes

Date: 2017-7-21

### Download

http://www.memcached.org/files/memcached-1.5.0.tar.gz

### Overview

This release makes the options `-o modern` enabled in 1.4.39 the default
options. It also adds --long-options (see --help|-h) if the platform supports
it and fixes a small bug.

People who were not running -o modern before should see wins in hit rate or
reductions in memory requirements. Please report any regressions.

### Fixes

  * fix for musl libc: avoid huge stack allocation

### New Features

This release is the culmination of many years of part-time work.

Memcached was introduced in 2003, two years before the first dual-core opteron
x86 processor. Since then computers have added huge numbers of cores and
vastly expanded memory.

Recently the cost ratio of memory to CPU has started to shift a bit. For a few
years memory prices will likely be inflated (though this always comes in
cycles). I've managed to keep memcached's performance and thread scalability
very high. Now it makes use of some extra CPU in background threads in order
to reduce memory usage. You may find you need half as much RAM to get the same
hit ratio, or it could be the same as what you have now.

The new features:

 * LRU crawler to background-reclaim memory. Mixed-TTL's and LRU reordering
   leaves many holes, making it difficult to properly size an instance.
 * Segmented LRU. HOT/WARM/COLD and background processing should try harder to
   keep semi-active items in memory for longer.
 * Automated slab rebalancing. Avoiding slab stagnation as objects change size
   over time.
 * Faster hash table lookups with murmur3 algorithm (though it's been so long
   this is now outdated again;)
 * Reduce memory requirements per-item by a few bytes here and there
 * Immediately close connections when hitting the connection limit, instead of
   hanging until a spot opens up.
 * Items larger than 512k (by default) are assembled by stacking multiple
   chunks together. Now raising the item size above 1m doesn't drop memory
   efficiency by spreading out slab classes.

These features have been slowly assembled, tuned, and tested behind feature
flags for far too long. Recently after finishing reworking the automated page
mover, I felt confident enough to flip the defaults around and call this thing
1.5.x.

`-o modern` will live on as new feature flags appear. Though they may not take
as long to migrate to the actual defaults.

If you want the old defaults, `-o no_modern` will work for a while.

Some old flags will be deprecated (old maxconns handling, and the inverse of
the byte-savings, for two) and eventually removed.

A great thanks goes out to Andrew Rodland (hobbs) for running production tests
of the adjusted slab page algorithm. Help from the community greatly reduces
the time it takes for me to find bugs and gain confidence in features.

Another great thanks to https://www.packet.net/ - Their donation of test
hardware has been invaluable while I run endless burn-in tests on various
features. It's crazy the weird bugs you find while shoveling tens of millions
of qps at a single instance while moving memory around randomly!

It'd be great to hear from users as well. It's been a long time and there are
a lot of key/value stores now. If this work is truly useful we'll keep it up.

### Contributors

The following people contributed to this release since 1.4.39.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.39..1.5.0` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.0

```
     2	dormando

```
