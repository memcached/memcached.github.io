### Memcached 1.6.11 Release Notes

Date: 2021-9-27

### Download

https://www.memcached.org/files/memcached-1.6.11.tar.gz

### Overview

Bugfixes, potential performance improvements, improvements to logging.
Includes some upstreamed changes for an upcoming feature.

### Fixes

  * Expose number of currently active watchers in stats
  * Configurable minimum supported TLS protocol version
  * core: fix hang bug in extstore
  * thread: use eventfd for worker notify if available
  * thread: per-worker-thread connection event queues
  * core: cache.c cleanups, use queue.h freelist
  * core: add queue.h to replace handrolled queues.
  * logger: simplify logging code
  * logger: avoid polling without watchers
  * Implement `LOG_CONNEVENTS` watcher flag for connection state transitions
  * Report item sizes for fetch, mutation, and eviction watchers
  * Fix typos in doc/code comments (`tem->item`, etc)


### New Features

`watch connevents` will show realtime log entries about client
connect/disconnect events.

### Contributors

The following people contributed to this release since 1.6.10.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.10..1.6.11` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.11

```
    11	dormando
     5	Kevin Lin
     1	Tyson Andre

```
