### Memcached 1.6.20 Release Notes

Date: 2023-5-12

### Download

http://www.memcached.org/files/memcached-1.6.20.tar.gz

### Overview

Lots of fixes and updates for Proxy mode. Optimization for extstore disk
flushing to alleviate excess evictions. Changes to meta protocol and smaller
fixes.

### Fixes

  * extstore: increase aggressiveness of flush thread
  * proxy: improve unit test coverage
  * proxy: random small fixes
  * proxy: return 'readvalidate' on be read timeout
  * proxy: don't print null ustats from "stats proxy"
  * proxy: use connect timeout during retries
  * proxy: fix flaky test in proxyconfig.t
  * Add a debian dockerfile, as well as upgrade the autoconf dependency
  * check for sys/auxv.h
  * proxy: send `CLIENT_ERROR` when proper
  * proxy: print lua error message on reload failure
  * proxy: rip out `io_uring` code (to be re-added later)
  * proxy: overhaul backend error handling: surface error messages to clients
  * proxy: fix reversal of pipelined backend queries
  * proxy: add request and buffer memory limits
  * proxy: restrict functions for lua config vs route
  * proxy: fix bug ignoring -R setting for proxy reqs
  * proxy: add conntimeout error

### New Features

  * proxy: add memory accounting tracking

Total in-flight item buffer memory can be tracked via `stats proxy` counters.
New options added for limiting the number of in-flight requests and amount of
buffer memory:
`mcp.active_req_limit(count)`
`mcp.buffer_memory_limit(kilobytes)`

These settings can be adjusted by reloading the configuration.

  * log: Add a new watcher to watch for deletions.

`watch deletions` stream for logs when items get deleted

  * meta: N flag changes append/prepend. 
  * meta: Add ms s flag.

Adds support for the `N` flag when running metasets in append mode. Normally
append/prepend mode does not work if the item doesn't already exist. Now it
will autovivify the item with the TTL supplied with the `N` flag.

Adds support for the `s` flag in metaset, which will return the size of the
new item stored. Useful with append/prepend.

### Contributors

The following people contributed to this release since 1.6.19.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.19..1.6.20` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.20

```
    28	dormando
     5	Fei Hu
     1	Fabrice Fontaine
     1	Hemal Shah
     1	Olof Nord

```
