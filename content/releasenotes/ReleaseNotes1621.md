### Memcached 1.6.21 Release Notes

Date: 2023-6-15

### Download

http://www.memcached.org/files/memcached-1.6.21.tar.gz

### Overview

Bugfix and community contribution release. As usual most fixes are for the new
proxy code, along with a lot of community contributed tests for proxy.

A data corruption issue for extstore was fixed. Requires overriding `item_age`
to a low number of seconds and re-writing the same keys frequently.

Fixes a meta protocol `ms` bug introduced in 1.6.20 with the `s` flag being
returned when only asking for the `c` (cas) flag.

Fixes a compilation issue for proxy due to build artifacts being accidentally
included into the release tarball.

### Fixes

  * build: avoid disting build artifacts from `vendor/*`
  * extstore: fix data bugs on high overwrite key
  * proxy: fixes for memory tracking
  * meta: fix ms c flag reflecting s flag
  * extstore: fail to start if given no disk space
  * extstore: Handle incorrect units gracefully
  * proxy: mcp.internal() support ascii multiget
  * proxy: fix segfault for reqs with too few tokens
  * proxy: fix per-worker-thread backend mode batching
  * proxy: fix meta set M flag for mcp.internal()
  * proxy: add await tests in proxyunits.t
  * proxy: add response API tests in proxyunits.t

### New Features

None

### Contributors

The following people contributed to this release since 1.6.20.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.20..1.6.21` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.21

```
     9	dormando
     2	Fei Hu
     1	Danny Kopping
     1	David Carlier
     1	KissPeter
     1	ajccosta

```
