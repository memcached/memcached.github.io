### Memcached 1.5.5 Release Notes

Date: 2018-2-12

### Download

http://www.memcached.org/files/memcached-1.5.5.tar.gz

### Overview

General bugfix release. Potential rare deadlock fixed (introduced in 1.5.x
series). Improvements to memory balancing when extstore is in use.

### Fixes

  * remove redundant counter/lock from hash table (2% boost for sets)
  * limit crawls for metadumper. avoids dumping too much data.
  * extstore: revise automove algorithm
  * quick fix for slab mover deadlock
  * extstore: fix segfault in 'extstore' admin command

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.4.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.4..1.5.5` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.5

```
     6	dormando

```
