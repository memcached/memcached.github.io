### Memcached 1.6.27 Release Notes

Date: 2024-5-5

### Download

http://www.memcached.org/files/memcached-1.6.27.tar.gz

### Overview

Many proxy updates, including a critical stability fix for users of the newer
style request API.

Adds new meta protocol extensions:

- `Ecas`: If specified and request succeeds, uses supplied number for the CAS
  value of the item. This allows using external versioning for cache data (ie;
  row versions, crc's, etc).

- `x` flag for `md`: This flag causes `md` to atomically replace the value
  with the same meta data, but 0 byte value. Can be combined with `I` to make
  an empty item that is marked stale.

### Fixes

  * proxy: fix backend depth counter going negative
  * proxy: fix short writes caused by mcp.internal
  * proxy: fix IO backlog softlock
  * proxy: even more lua GC tuning
  * proxy: fix race condition leading to hang
  * proxy: fix possible corruption with global objects
  * extstore: start arg tokenizer fix
  * proxy: fix `proxy_config` with > 2 lua files

### New Features

  * core: add queue info to `stats conns`
  * proxy: `mcp.server_stats(subcmd)`
  * meta: md with x removes value
  * proxy: wait timeout API
  * proxy: config thread cron functions
  * meta: E flag for overriding CAS value
  * proxy: `stats proxybe` to show bad backends
  * proxy: rctx:cfd() and `log_req` cfd

### Contributors

The following people contributed to this release since 1.6.26.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.26..1.6.27` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.27

```
    19	dormando

```
