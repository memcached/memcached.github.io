### Memcached 1.6.2 Release Notes

Date: 2020-3-23

### Download

http://www.memcached.org/files/memcached-1.6.2.tar.gz

### Overview

Fixes a remote DoS (segfault) in parsing of the binary protocol header that was
introduced in 1.6.0. Update immediately or disable the binary protocol if you
are not using it (`-B ascii`).

### Fixes

 * No other fixes.

### New Features

 * None.

### Contributors

The following people contributed to this release since 1.6.1.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.1..1.6.2` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.2

```
     1	dormando

```
