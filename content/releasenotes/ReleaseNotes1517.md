### Memcached 1.5.17 Release Notes

Date: 2019-8-29

### Download

http://www.memcached.org/files/memcached-1.5.17.tar.gz

### Overview

Bugfixes and small changes. Thanks to the many external contributors in this
release!

The strncpy bug was found by a security group:

 *  Jonas Jensen
 *  Geoffrey White
 *  Antonio Morales

... though given the nature of the bug, while it will trip ASAN, there's no
way to exploit it and it only occurs over unix domain sockets. No data is
copied past the end of any buffers. Still, we take this seriously and have
repaired the offending code, just in case.

### Fixes

  * fix strncpy call in `stats conns` to avoid ASAN violation
  * extstore: fix indentation
  * add error handling when calling dup function
  * add unlock when item_cachedump malloc failed
  * extstore: emulate pread(v) for macOS
  * fix off-by-one in logger to allow CAS commands to be logged.
  * use strdup for explicitly configured slab sizes
  * move mem_requested from slabs.c to items.c (internal cleanup)

### New Features

  * add server address to the "stats conns" output
  * log client connection id with fetchers and mutations
  * Add a handler for seccomp crashes

### Contributors

The following people contributed to this release since 1.5.16.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.16..1.5.17` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.17

```
     5	dormando
     4	Stanisław Pitucha
     2	Tharanga Gamaethige
     2	minkikim89
     1	Andrew Drake
     1	David Carlier
     1	Gregor Jasny
     1	Shiv Nagarajan

```
