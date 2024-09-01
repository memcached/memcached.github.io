### Memcached 1.5.14 Release Notes

Date: 2019-4-27

### Download

https://www.memcached.org/files/memcached-1.5.14.tar.gz

### Overview

Bugfix release. No new features. Fix an extstore test failure introduced in 1.5.13. All other bugs are trivial or older.

### Fixes

  * update -h output for -I (max item size)
  * fix segfault in "lru" command
  * fix compile error on centos7
  * extstore: error adjusting `page_size` after `ext_path`
  * extstore: fix segfault if `page_count` is too high.
  * close delete + incr item survival race bug
  * FreeBSD superpages checking.
  * FreeBSD capabilities support.
  * memcached-tool dump fix loss of exp value
  * Add optional feature support to RPM package building
  * Fix "qw" in "MemcachedTest.pm" so `wait_ext_flush` is exported properly

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.13.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.13..1.5.14` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.14

```
     8	dormando
     2	David Carlier
     1	John Leslie
     1	Tianon Gravi
     1	hayashier
     1	miwasson

```
