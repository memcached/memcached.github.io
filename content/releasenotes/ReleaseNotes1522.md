### Memcached 1.5.22 Release Notes

Date: 2020-2-1

### Download

http://www.memcached.org/files/memcached-1.5.22.tar.gz

### Overview

Bugfix release for a crash regression introduced in 1.5.20. An optimization
made to the slab page mover in 1.5.20 could cause segfaults in a specific
situation. 

When moving page memory containing a chunked (> 512K) item header,
and the item is expired, but not yet reaped by LRU crawler or other
mechanisms, the attached chunks would leak. If later the page mover tries to
move the memory for these page chunks, it will no longer be able to find the
proper header item, eventually causing a crash.

Thanks to the folks on github who reported this bug!

### Fixes

  * slabs: fix crash in page mover
  * slabs: fix for skipping completed items

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.21.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.21..1.5.22` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.22

```
     3	dormando

```
