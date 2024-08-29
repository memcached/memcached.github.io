### Memcached 1.6.18 Release Notes

Date: 2023-1-10

### Download

http://www.memcached.org/files/memcached-1.6.18.tar.gz

### Overview

Mostly fixes and improvements to proxy mode.

Meta protocol has some adjusments: extra spaces were being returns in a few
instances. If your client is looking for an exact number of spaces in meta
responses please take note. We regret the error.

### Fixes

  * core: do not stop threads on SIGINT/SIGTERM
  * core: remove printf from signal handlers
  * proxy: iterate modified request handling
  * proxy: expose resp:elapsed()
  * proxy: log time now relative to resp lifetime
  * proxy: fix crash in await during SIGHUP reloads
  * proxy: fix lifecycle of backend connections
  * proxy: IO thread performance improvements
  * proxy: add `mcp.AWAIT_BACKGROUND`
  * proxy: fix lua registry corruption on data chunk error
  * proxy: add `proxy_await_active` stat
  * proxy: fix partial response read handling
  * proxy: fix flushing partial writes
  * proxy: add more backend failure messages
  * proxy: fix `mcp.log_req` crash on nil res
  * core: fix tagged listeners for len < 8
  * proxy: add debug symbols to lua build
  * core: give threads unique names
  * proxy: fix crash when backends are gc'd
  * remove libevent license from usage
  * Fixes to build with clang-15:
  * meta: remove `meta_response_old` start option
  * meta: allow mg without flags + reflect O/k on EN
  * meta: meta arithmetic command had excess spaces
  * meta: remove excess spaces from meta responses
  * proxy: fix bugs with backend connection initialization
  * Fix log timestamps after 2038
  * Fix function protypes for clang errors
  * proxy: add mcp.await FASTGOOD flag

### New Features

See the [Proxy wiki page](/Proxy) page for updates to the proxy API.

### Contributors

The following people contributed to this release since 1.6.17.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.17..1.6.18` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.18

```
    33	dormando
     1	Bernhard M. Wiedemann
     1	David Bohman
     1	David Carlier
     1	Khem Raj

```
