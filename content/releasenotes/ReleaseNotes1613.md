### Memcached 1.6.13 Release Notes

Date: 2022-1-12

### Download

http://www.memcached.org/files/memcached-1.6.13.tar.gz

### Overview

Many important bugfixes in this release. Includes fixes to the meta protocol,
fix for large text multigets in certain conditions, and a rare slowdown in
extstore.

Also included in the codebase is a non production ready test version of an
integrated proxy. For adventerous developers see `t/proxy.t` test code and the
lua libraries at https://github.com/memcached/memcached-proxylibs - for
everyone else, hold on for future versions.

### Fixes

  * core: make object cache LIFO
  * meta: protocol.txt updates for CAS return
  * meta: fix meta delete
  * meta: fix CAS ('c') return values
  * core: fix use-after-free for text multigets
  * Replace `OPENSSL_VERSION_NUMBER` check with `defined(TLS1_3_VERSION)` check for TLS v1.3 compatibility
  * Fix full unit test suite under `test_tls`
  * Track store errors in thread stats
  * Fix for failing tests on OS X
  * extstore: avoid looping IO queues on submission
  * tests: maxconns test when extstore enabled
  * core: remove cdefs include from queue.h


### New Features

The primary feature in development at this time is the proxy. Work can be
tracked here: https://github.com/memcached/memcached/issues/827 - As this
stabilizes formal documents will be written. If you're curious about the
feature please speak up as we are looking for testers and contributors!

### Contributors

The following people contributed to this release since 1.6.12.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.12..1.6.13` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.13

```
    16	dormando
     3	Kevin Lin
     1	kokke

```
