### Memcached 1.5.11 Release Notes

Date: 2018-10-10

### Download

http://www.memcached.org/files/memcached-1.5.11.tar.gz

### Overview

Fixes broken test and small extstore optimization.

### Fixes

  * extstore: balance IO thread queues
  * t/lru-maintainer.t: check for WARM item earlier, fixing race condition on
    some platforms.

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.10.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.10..1.5.11` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.11

```
     2	dormando
     1	Nick Frost

```
