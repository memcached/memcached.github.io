### Memcached 1.6.6 Release Notes

Date: 2020-5-12

### Download

http://www.memcached.org/files/memcached-1.6.6.tar.gz

### Overview

Bugfixes mostly relating to portability and packaging. Many thanks to the
various package maintainers who wrote in and sent patches.

### Fixes

  * Update testing certificates to be compatible with latest security levels
  * Fix crash on shutdown when handling signals with TLS enabled
  * start of valgrind test mode
  * more flaky test fixes; crawler/restart
  * Disable aarch64 hw crc32 function for now
  * Pull in BigEndian-compatible crc32c
  * minimum libevent version is 2.x
  * Add Meta no-op command request to protocol.txt

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.5.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.5..1.6.6` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.6

```
    12	dormando
     2	Tomas Korbar
     1	Dmitry Volodin
     1	Jefty Negapatan

```
