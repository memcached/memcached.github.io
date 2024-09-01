### Memcached 1.5.16 Release Notes

Date: 2019-5-24

### Download

http://www.memcached.org/files/memcached-1.5.16.tar.gz

### Overview

Fixes critical potential segfault/memory corruption bug in 1.5.15 when storing
items with client flags of "0". Bug only exists in 1.5.15, and the fix is the
only change in this release.

### Fixes

  * When nsuffix is 0 space for flags hasn't been allocated so don't memcpy them.

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.15.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.15..1.5.16` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.16

```
     1	Matthew Shafer

```
