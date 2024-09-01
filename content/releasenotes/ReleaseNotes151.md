### Memcached 1.5.1 Release Notes

Date: 2017-8-24

### Download

http://www.memcached.org/files/memcached-1.5.1.tar.gz

### Overview

Bugfix release for the 1.5.0 series. Includes `--enable-seccomp` configure
option for enabling linux seccomp privilege dropping.

### Fixes

  * add `max_connections` stat to 'stats' output
  * Drop sockets from obviously malicious command strings (HTTP/)
  * stats cachedump: now more likely to show data
  * memcached-tool: fix slab `Full?` column
  * fix null pointer ref in logger for bin update cmd
  * default to unix sockets for tests, make them much less flaky
  * PARALLEL=9 make test -> runs prove in parallel
  * fix flaky stats.t test


### New Features

--enable-seccomp compiles in options for strict privilege reduction in linux.
see output of `-h` for more information.

### Contributors

The following people contributed to this release since 1.5.0.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.0..1.5.1` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.1

```
    10	dormando
     4	Stanisław Pitucha
     1	Ben Evans
     1	David Schoen

```
