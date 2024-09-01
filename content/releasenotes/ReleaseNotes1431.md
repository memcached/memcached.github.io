### Memcached 1.4.31 Release Notes

Date: 2016-8-19

### Download

http://www.memcached.org/files/memcached-1.4.31.tar.gz

### Overview

Big new feature: `lru_crawler metadump all`, will now dump (most of) the metadata
for (all of) the items in the cache. As opposed to cachedump, it does not
cause severe performance problems and has no limits on the amount of keys that
can be dumped.

### Fixes

  * More fixes for defaults related to large item support.
  * Several improvements to how the LRU crawler's default background job is
    launched. Should be less aggressive.
  * Fix LRU crawler rate limiting sleep never actually being used.

### New Features

If `-o modern` is in use (or at least `-o lru_crawler`), a new command is
usable: `lru_crawler metadump [all|1|2|3]`. You may dump all of the metadata for a
particular slab class, a list of slab classes, or all slab classes.

This is useful for inspection, pulling cache data from one server to another,
etc.

This is not a way to replicate a cache: it is an inconsistent
non-multiversioned grab of the key storage. It makes a best effort to not
include duplicates but a small number can happen on busy servers.

Many thanks for Netflix, for sponsoring and patiently testing the work done in
this release!

More details and code can be seen in the PR:
https://github.com/memcached/memcached/pull/193

### Contributors

The following people contributed to this release since 1.4.30.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.30..1.4.31` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.31

```
    14	dormando

```
