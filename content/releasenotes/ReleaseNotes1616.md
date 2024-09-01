### Memcached 1.6.16 Release Notes

Date: 2022-8-3

### Download

http://www.memcached.org/files/memcached-1.6.16.tar.gz

### Overview

Minor bugfixes for memcached daemon.
Critical bugfixes and API additions for the experimental proxy code (if using,
please upgrade)

### Fixes

  * proxy: add `req:flag_token("F")`
  * proxy: mcp.response code and rline API
  * proxy: add `r:has_flag()`, fix r:token() length
  * proxy: mcp.request() improvements
  * proxy: `mcplib_request_token()` doesn't delimit the final token in a request
  * tls: Disable TLS re-negotiation from SSL context
  * Fix undefined behavior and warning with clang
  * proxy: fix the hashstring size for evcache ketama
  * core: Fix FTBFS with GCC 12 on ppc64el
  * proxy: fix race crash from io obj use-after-free
  * proxy: fix mcp.await() when using extended args
  * proxy: add missing errno.h include to proxy.h
  * proxy: fix potential corruption on partial write
  * proxy: rework backend buffer handling to fix protocol desync bug
  * tests: skip whitespace on `vendor/*`
  * tls: Add switch to opt-in to kernel TLS on OpenSSL 3.0.0+
  * core: checks port number at start time
  * Add a command to dump keys for memcached-tool
  * proxy: 'proxyreqs' does not work unless 'proxyuser' also provided
  * proxy: replace proxycmds stream with proxyreqs
  * proxy: `mcp.log_req*` API interface


### New Features

None, outside of API additions to the proxy code.

### Contributors

The following people contributed to this release since 1.6.15.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.15..1.6.16` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.16

```
    15	dormando
     2	Sailesh Mukil
     1	David Carlier
     1	Junji Hashimoto
     1	Kevin Lin
     1	Prudhviraj K
     1	Qu Chen
     1	Raphael Isemann
     1	Sergio Durigan Junior
     1	Tom Stellard

```
