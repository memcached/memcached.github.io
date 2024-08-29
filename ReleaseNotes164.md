### Memcached 1.6.4 Release Notes

Date: 2020-4-12

### Download

http://www.memcached.org/files/memcached-1.6.4.tar.gz

### Overview

Bugfix release. Notably compile/build/compat fixes. Also fixes a few failures
in the restartable mode when using chunked items.

### Fixes

  * restart: fix failure on deleted chunked items
  * ascii auth: fix CPU spike when waiting for data
  * extstore: fix some valgrind errors.
  * Fix undefined behavior with `-D_FORTIFY_SOURCE=2`
  * Fix build warnings in Windows.
  * Add build option to disable unix socket functionality.
  * fix extstore reads for OSX/cygwin
  * Fix misprint in protocol.txt
  * Fix t/64bit.t test failure in Windows.
  * build: sasl build fix on FreeBSD
  * testapp: Fix failure with -flto=auto

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.3.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.3..1.6.4` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.4

```
     8	dormando
     3	Jefty Negapatan
     1	David Carlier
     1	Dmitry Volodin
     1	Kanak Kshetri

```
