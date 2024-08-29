### Memcached 1.4.39 Release Notes

Date: 2017-7-4

### Download

http://www.memcached.org/files/memcached-1.4.39.tar.gz

### Overview

Fixes a reported CVE in binary protocol code. Also adds a trivial 4 byte
reduction in memory usage under `-o modern` if client flags aren't used.

This CVE seems relatively minor; may be able to crash worker threads. I wasn't
able to quickly reproduce a crash but folks should still upgrade when
convenient.

### Fixes

  * fix for CVE-2017-9951
  * save four bytes per item if client flags are 0


### New Features

If client flags are "0", no extra storage is used.

### Contributors

The following people contributed to this release since 1.4.38.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.38..1.4.39` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.39

```
     2	dormando

```
