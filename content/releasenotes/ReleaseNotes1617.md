### Memcached 1.6.17 Release Notes

Date: 2022-8-26

### Download

http://www.memcached.org/files/memcached-1.6.17.tar.gz

### Overview

Lots of fixes and a few new features in this release. Lots of proxy updates as
usual for the last few. Core fixes include better defaults and higher
reliability for the memory mover, extstore, and large item support.

### Fixes

  * Release TLS read and write buffers when idle
  * Find perl via /usr/bin/env instead of directly
  * Mac M1 build update. detects arm64 crc32 h/w support.
  * DTrace build fix on Mac
  * core: fix strncat warning
  * configure.ac: use pkg-config to retrieve openssl
  * proxy: fix missing md5.h from tarball dist
  * docs: don't rebuild binprot XML anymore
  * Do memory bound check for some C string operations
  * proxy: allow mcp.pool to ignore a nil second arg
  * Improve Slab Automove behavior
  * proxy: allow booleans in pool structure
  * proxy: backend object cache was broken
  * log: fix obscure crashes due to `size_t` promotion
  * Fix race leads to deadlock during shutdown (sigterm/sigusr1)
  * proxy: `req:flag_token("F", "Freplacement")`


### New Features

  * sock ip filtering tagging support for FBSD/OBSD
  * MacOS drop privileges support
  * core: make large item storage more reliable
  * extstore: make defaults more aggressive

Previously when using external (SSD/disk) storage, a number of items could be
evicted when RAM first fills and the background flush threads have not kicked
in yet. This should be largely mitigated.

For large items, if used exclusively, there were edge cases where SETs would
fail because of memory exhaustion in smaller slab classes. This should be
largely fixed.

Proxy features are documented at: https://github.com/memcached/memcached/wiki/Proxy

### Contributors

The following people contributed to this release since 1.6.16.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.16..1.6.17` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.17

```
    16	dormando
     4	David CARLIER
     1	David Carlier
     1	Fabrice Fontaine
     1	Iliya
     1	Qu Chen
     1	Sailesh Mukil
     1	Tharanga Gamaethige

```
