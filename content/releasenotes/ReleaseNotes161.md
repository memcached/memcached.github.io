### Memcached 1.6.1 Release Notes

Date: 2020-3-16

### Download

http://www.memcached.org/files/memcached-1.6.1.tar.gz

### Overview

Bugfix release. Catches a number of build errors that crept in during 1.6.0.
Also fixes most of the flaky tests in the test suite, making memcached more
usable with slower CI builders.

Thanks to the many contributors to this release!

### Fixes

  * tls: handle accept errors properly
  * `ssl_errors` stat
  * Add touch,gat expiration test
  * Add negative expiration time case to expiration test
  * Fix to handle negative exptime as common function
  * Fix a bunch of flaky tests
  * Fix compile error with DTrace probes
  * Allow missing syscall on ARM for seccomp
  * fix multiple definition of 'crc32c' (GCC 10)
  * Add stdio.h,stddef.h to storage.c (OS X compile error)

A few text commands did not handle negative expiration times properly. This
has been fixed to ensure they immediately expire, instead of just usually
immediately expiring.

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.0.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.0..1.6.1` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.1

```
    11	dormando
     4	minkikim89
     1	David Carlier
     1	Kevin Lin
     1	Remi Collet
     1	Shiv Nagarajan

```
