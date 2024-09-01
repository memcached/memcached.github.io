### Memcached 1.4.25 Release Notes

Date: 2015-11-19

### Download

http://www.memcached.org/files/memcached-1.4.25.tar.gz

### Overview

Many small fixes from internal and public pull requests/bug reports have gone
into this release. Thanks to all of those who contribute.

A large change to slab class rebalancing has gone into this release. Please
read carefully below and try the new options, as they may improve your memory
efficiency and hit ratio. Especially for long running instances.

### Fixes

  * Automake improvements
  * Misc documentation fixes
  * Misc updates to startup scripts
  * `lru_crawler enable` blocks until ready (test failure)
  * Record and report on time spent in `listen_disabled`
    (time_in_listen_disabled_us)
  * Update manpage for -I command. Make it more clear
  * Fix display of `settings.hot_lru_pct` in `stats settings`
  * No longer edits the output of `ps` while processing arguments
  * No longer crashes when failing to give arguments to some start args
  * Fix memcached unable to bind to an ipv6 address
  * No longer attempts bind to same interface more than once
  * fixed libevent version check: add the missing 1.0.x version check
  * fix off-by-one in LRU crawler, causing rare segfault
  * remove another invalid assert(), fixes clang and pedantic compilation

### New Features

Slab automover has gotten a very large update. The wider discussion can be
found in the [pull request](https://github.com/memcached/memcached/pull/113).

As data is stored into memcached, it pre-allocates pages of memory into slab
classes of a particular size (ie: 90 bytes, 120 bytes). If you fill your cache
with 90 byte objects, and then start writing 120 byte objects, there will be
much less space available for 120 byte objects.

With the slab automover improvements, freed memory can be reclaimed back into
a global pool and reassigned to new slab classes. You can also still manually
move slab pages between classes with your own external process if the automover
does not fit your needs (see doc/protocol.txt for full details).

The automover now attempts to rescue items which are still valid when moving a
page from one class to another, or from one class into the global page pool.
This makes it much less destructive.

To get all of the benefits of the last few releases, we recommend adding the
following startup options:

`-o slab_reassign,slab_automove,lru_crawler,lru_maintainer`

A modern start line includes a few other items:

`-o slab_reassign,slab_automove,lru_crawler,lru_maintainer,maxconns_fast,hash_algorithm=murmur3`

Many of these options are likely to become defaults in the future.

### Contributors

The following people contributed to this release since 1.4.24.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.24..1.4.25` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.25

```
    20	dormando
     3	Cameron Norman
     3	Miroslav Lichvar
     2	Antony Dovgal
     1	Alwayswithme
     1	Ian Miell
     1	Johan Bergström
     1	Mattias Geniar
     1	Roman Mueller
     1	Saman Barghi
     1	Sharif Nassar
     1	Yongyue Sun
     1	githublvv
     1	kenvifire
     1	mdl
     1	wangkang-xy
     1	zhoutai

```
