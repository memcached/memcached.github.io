### Memcached 1.6.19 Release Notes

Date: 2023-3-8

### Download

http://www.memcached.org/files/memcached-1.6.19.tar.gz

### Overview

Many fixes and new features for the Proxy feature. Rest are small fixes or
code cleanups to the cache daemon. If you're trying out the Proxy code, please
upgrade or continue to track the `next` branch.

If you use the `lru_crawler metadump hash` command, specifically the hash
mode, there is a fix for potential slowdowns.

`-Werror` is no longer the default when compiling. Add `--enable-werror` to
restore the feature.

### Fixes

  * replace 2&>1 by 2>&1 in rpm spec file
  * log: fix race condition while incrementing log entries dropped
  * Add new pkg-config dependencies to dockerfiles
  * Document missing flags of Meta Arithmetic
  * configure.ac: add --enable-werror
  * proxy: reduce noise for dead backends
  * proxy: more await unit tests
  * proxy: fix trailingdata error with ascii multiget misses
  * crawler: don't hold lock while writing to network
  * proxy: redo libevent handling code (speedup/fixes)
  * proxy: fix "missingend" error on reading responses
  * proxy: add read buffer data to backend error messages
  * proxy: fix partial responses on backend timeouts
  * proxy: disallow overriding mn command
  * tests: timedrun SIGHUP pass-thru
  * proxy: new integration tests.
  * proxy: fix mismatched responses after bad write
  * proxy: fix stats deadlock caused by await code
  * proxy: clean logic around lua yielding
  * core: remove `*c` from some response code
  * core: simplify background IO API
  * core: remove `*conn` object from cache commands

### New Features

  * crawler: add `lru_crawler mgdump` command

For more efficiently getting the keylist back out of the server. A utility
demonstrating its usage can be found here:
https://github.com/memcached/mcdumpload

  * proxy: allow workers to run IO optionally

If `iothread = false` is added as an option to `mcp.pool()`, each worker
thread will handle its own backend IO for this pool. Instead of one tcp
connection there will be one per worker thread. This allows mixing and
matching (ie; use worker threads for high data rate backends, use IO thread
for background IO's)

  * proxy: add mcp.backend(t) for more overrides

See tests, examples, documentation. Allows overriding timeouts, number of
retries, and so on, on a per-backend basis.

  * proxy: add `mcp.await_logerrors()`

New form of `mcp.await()` which emits a log line for every response that ends
in an error, _after_ the main request has returned

  * proxy: add mcp.internal(r) API

An experimental API for accessing the cache server embedded in every proxy.
Executes the command and retrieves a response object as though the command
were executed against a remote backend. This allows creating pools of proxies
which can answer responses directly, while doing replication to remote zones
and other tasks.

### Contributors

The following people contributed to this release since 1.6.18.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.18..1.6.19` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.19

```
    28	dormando
     5	xuesenliang
     1	David Carlier
     1	Fabrice Fontaine
     1	Mate Borcsok
     1	Olof Nord
     1	Patrice Duroux
     1	Ramasai

```
