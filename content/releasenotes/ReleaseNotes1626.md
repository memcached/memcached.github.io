### Memcached 1.6.26 Release Notes

Date: 2024-3-27

### Download

http://www.memcached.org/files/memcached-1.6.26.tar.gz

### Overview

Fixes a crash when `lru_crawler metadump` is used and the client connection is
closed early. Requires closing the client excactly before the last few
kilobytes of a dump are about to be flushed.

Also improves the ergonomics of proxy configurations via start arguments.

Please see the [1.6.25 Release Notes](/ReleaseNotes1625) for a recent
significant improvement to Extstore.

See [Proxy](/Proxy) for details and quick start guides on the internal Proxy.

### Fixes

  * proxy: -o `proxy_arg` to pass cmdline to lua
  * proxy: -o `proxy_config` takes multiple files
  * crawler: fix potential memory corruption

### New Features


### Contributors

The following people contributed to this release since 1.6.25.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.25..1.6.26` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.26

```
     3	dormando

```
