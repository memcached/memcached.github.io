### Memcached 1.6.22 Release Notes

Date: 2023-10-15

### Download

http://www.memcached.org/files/memcached-1.6.22.tar.gz

### Overview

Contains important security fixes for users of the proxy, please upgrade at
your earliest convenience. This does not affect you unless you build with
`--enable-proxy` _and_ enable the proxy at start time.

Contains many fixes and optimizations foe the proxy subsystem, along with a
few small fixes to various things.

### Fixes

  * core: fix incr/decr/ma failing in some conditions
  * extstore: fix item loss during page defrag
  * Note that counts for `track_sizes` are best effort
  * core: fix build on BSD
  * core: speedup for async IO handling
  * core: don't ignore sighup in daemonize mode
  * proxy: add label to proxy backend error logs
  * proxy: if a ustat label is "", skip printing
  * proxy: fix `ms` ignoring `T` flag for mcp.internal
  * proxy: fix `ms` parsing error
  * proxy: fix backend cleanup when using worker IO
  * proxy: fix response code in the event of dead backend
  * proxy: fix race condition in first load
  * proxy: fix off-by-one if \r is missing
  * proxy: update backends if conncount changes
  * proxy: io submission queue opt
  * proxy: io return optimization
  * proxy: fix for dropping -O2 from lua compile
  * proxy: fix buffer overflow with multiget syntax
  * proxy: fix flaky test in t/proxylimits.t

### New Features

  * proxy: backend antiflap detection.
  * proxy: mcp.backend({ down = true }) option
  * proxy: add `mcp.ratelim_tbf({})`
  * proxy: easier usage of N sockets per backend

See commit history for full notes. Also see the proxy documentation at: https://github.com/memcached/memcached/wiki/Proxy

### Contributors

The following people contributed to this release since 1.6.21.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.21..1.6.22` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.22

```
    30	dormando
     3	Finn Frankis
     1	Fei Hu
     1	Nick Pillitteri

```
