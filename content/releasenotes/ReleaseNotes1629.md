### Memcached 1.6.29 Release Notes

Date: 2024-6-28

### Download

https://www.memcached.org/files/memcached-1.6.29.tar.gz

### Overview

Only proxy related updates.

### Fixes

  * proxy: ustats internal refactor
  * proxy: router refactor and cmap fallback
  * proxy: ensure resp.elapsed gets set on backend errors
  * proxy: let proxy build without openssl installed

### New Features

All proxy updates:
- Speeds up displaying `stats proxy` when many user defined counters are used.
- If a user counter changes name, it will be reset to zero.
- Allows overriding commands either by key prefix or specific command.

These are supported by routelib: https://github.com/memcached/memcached-proxylibs/tree/main/lib/routelib

### Contributors

The following people contributed to this release since 1.6.28.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.28..1.6.29` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.29

```
     5	dormando

```
