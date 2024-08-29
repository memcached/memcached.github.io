### Memcached 1.4.35 Release Notes

Date: 2017-2-26

### Download

http://www.memcached.org/files/memcached-1.4.35.tar.gz

### Overview

Significant fixes to new LRU algorithm. Also performance and memory overhead
improvements. You are even more likely to see improved hit ratios when using
`-o modern`. Please try it out!

Looking for contributors! The relevant wiki has been updated:
https://github.com/memcached/memcached/wiki/DevelopmentRepos

If you use memcached, please consider helping out! Much appreciation to
those who already do.

This version is getting close to what I envision 1.5.0 will be. The plan for
1.5.0 (as of this writing) is to simply switch the defaults to what is
reflected in `-o modern`, no or minimal other changes will be included in that
release.

To prepare yourself for the 1.5.0 release, again please try one instance with
`-o modern` options.

### Fixes

  * init.d script status check routine
  * Print with more-restricted format string to fix compiler warning with gcc 7's -Wformat-truncation.
  * Display HOT/WARM tail age in `stats items`
  * Active items in HOT` flow to WARM (algorithm fix)
  * Moves to WARM requires two hits overall (algorithm fix)
  * LRU maintainer performance: per-class sleep scheduling
  * Allow limiting the internal LRU crawler run length
  * Stop using atomics for item refcount management (performance)
  * Make the conn suffix list the same as item list (performance)
  * Do LRU-bumps while already holding item lock (performance)
  * Reduce add_iov() work for TCP connections (performance)

### New Features

  * "lru" command for setting LRU parameters at runtime
  * Allow switching LRU algo's at runtime

The full documentation is in doc/protocol.txt under "LRU Tuning". Since a few
years ago, memcached supports the original "flat" LRU algorithm alongside a
segmented LRU. With the "lru" ASCII command you can tune settings live, or
even switch between algorithms entirely!

So long as memcached is started with `-o modern` or similar, you can
experiment with the hit ratio of differnt algorithms or options without
restarting with a cold cache each time.

IE: `lru mode flat` to switch back from segmented. Or `lru mode segmented` to
flip back again.

`lru tune [etc]` is for adjusting the segmented LRU options. I strive for
sensible defaults, but that may not apply to everyone.

  * New `TEMP_LRU` feature

The "expirezero does not evict" feature is gone, and in its place is a
"temporary LRU". Items set with TTL's under a specific value are put into a
special "temporary" LRU. These items are not evictable, and the LRU maintainer
background thread reaps from the tail as they expire.

This can help a lot if you have mixed workloads with short and long TTL's, as
items with short TTL's could be freed automatically sooner than any other
background process. Do not set this too high!

This can be enabled with `-o temporary_ttl=60` for a "60 seconds or lower", or
live via `lru temp_ttl 60`. `lru temp_ttl -1` disables the feature live.

  * autotuning for HOT/WARM LRU's

By default the HOT and WARM LRU's can take up to 32% of memory each. I've
found people with mixed TTL workloads will end up with close to 1/3rd of
memory utilized. With this change a couple more tunables (adjustable live via
`lru tune`) are added, with hopefully sensible defaults.

Items are now moved out of HOT if they are idle more than 3600 seconds _or_
memory usage is above 32%.

Items are now moved out of WARM if they are idle more than twice the idle time
of COLD, _or_ memory usage is above 32%. This is to encourage fairness between
items that were active briefly but never again and newer items flowing through
COLD. Sometimes WARM idle time could reach weeks!

  * Conditionally don't inline the ASCII VALUE line (memory
    efficiency/performance)

Since the first releases, memcached has written an inlined buffer of the ASCII
protocol response into items. This uses some small amount of memory in
exchange for avoiding a sprintf on every read. In this release, a very fast
itoa library is used and the header is written into a temporary buffer.

This means ASCII reads are very slightly slower, however it also means SETs
are faster. Also the binary protocol never used this header, so binprot SETs
are improved and memory efficiency is improved for small objects.

A SET would usually sprintf twice. Other performance tweaks were included,
this release should be faster overall than any previous one. Currently this is
enabled within `-o modern` or `-o no_inline_ascii_resp`.

### Contributors

The following people contributed to this release since 1.4.34.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.34..1.4.35` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.35

```
    34	dormando
     1	Joe Orton
     1	Juliy V. Chirkov

```
