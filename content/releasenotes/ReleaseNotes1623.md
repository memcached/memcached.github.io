### Memcached 1.6.23 Release Notes

Date: 2024-1-9

### Download

http://www.memcached.org/files/memcached-1.6.23.tar.gz

### Overview

This release only affects the proxy code. The request dispatch API has been
redone. See https://github.com/memcached/memcached/wiki/Proxy for full
documentation of the API.

In this release please consider the new API _SLIGHTLY EXPERIMENTAL_. While a
large effort has been made to validate the code, it is a big change. Please
carefully test before deploying.

If you are new to the proxy, a new user friendly route library is being
developed:
https://github.com/memcached/memcached-proxylibs/tree/main/lib/routelib

_PLEASE NOTE_: The previous `mcp.await` and `pool(request)` call forms are being
aggressively deprecated. The code will be removed in the next release of
memcached. You will need to move your configuration scripts to the new API
before upgrading again. We do not expect to have to do this again in the
future.

This is being done to allow simplifying the internal code and unblock
performance and stabilization features that the old API was preventing.

_DEFAULT CHANGE_: The proxy has, by default, used a single background thread
for issuing IO to backend servers. This generally prevents scaling past 4 CPU
cores, but reduces the number of TCP sockets in use and increases pipelining
to backends.

As of this release the default is to issue backend IO directly from worker
threads. This behavior can be tweaked both on a global or per-pool basis.

It is our goal to stabilize the proxy going forward, focusing on code cleanups
and smaller changes from now on. We also aim to include the new `routelib`
directly inside memcached in the next release to improve ease of use.

### Fixes

  * proxy: add `mcp.backend_use_iothread(bool)` + bugfix
  * proxy: lua API version 2
  * proxy: add `mcp.time_[real|mono]_millis()`

### New Features

Proxy API version 2:

- Improved performance by allowing pre-calculations reused on each request
- Improved performance by avoiding allocations at request time, preventing GC usage
- Allow recursive function calls
- Allow much easier future API expansion
- Custom callbacks for every backend request issued

### Contributors

The following people contributed to this release since 1.6.22.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.22..1.6.23` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.23

```
     5	dormando

```
