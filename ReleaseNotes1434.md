### Memcached 1.4.34 Release Notes

Date: 2017-1-7

### Download

http://www.memcached.org/files/memcached-1.4.34.tar.gz

### Overview

Larger than average bugfix release. :) Important fix for users of SASL
authentication.

### Fixes

  * Add `-o modern` switches to -h
  * metadump: Fix preventing dumping of class 63
  * Fix cache_memlimit bug for > 4G values
  * metadump: ensure buffer is flushed to client before finishing
  * Number of small fixes/additions to new logging
  * add logging endpoint for LRU crawler
  * evicted_active counter for LRU maintainer
  * stop pushing NULL byte into watcher stream
  * Scale item hash locks more with more worker threads (minor performance)
  * Further increase systemd service hardening
  * Missing necessary header for atomic_inc_64_nv() used in logger.c (solaris)
  * Fix print format for idle timeout thread
  * Improve binary sasl security fixes
  * Fix clang compile error
  * Widen systemd caps to allow maxconns to increase
  * Add -X option to disable cachedump/metadump
  * Don't double free in lru_crawler on closed clients
  * Fix segfault if metadump client goes away


### New Features

None

### Contributors

The following people contributed to this release since 1.4.33.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.33..1.4.34` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.34

```
    21	dormando
     1	Alexander Pyhalov
     1	Craig Andrews
     1	Nick

```
