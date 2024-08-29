### Memcached 1.5.21 Release Notes

Date: 2020-1-21

### Download

https://www.memcached.org/files/memcached-1.5.21.tar.gz

### Overview

Bugfixes and smaller changes. Thanks to all the external contributors!

### Fixes

  * Adding missing defaults to the --help output
  * stats: Fix stats delimiter unit tests
  * Allow compilation with ASAN
  * restart: add error handling if mmap fails
  * For text auth token mode, use alternative bcmp implementation
  * memcached-tool: Fix up tabular output for the 'stats' command.
  * `linux_priv.c`: add termios.h include to fix powerpc(64) builds
  * Update the build documentation in BUILD file
  * Update documentation for --max-item-size
  * Fix build issue due to improper `pthread_t` comparison
  * Ensure t/whitespace.t test is skipped when outside a memcached git checkout
  * Allocating large chunk of slabs for FreeBSD.

### New Features

  * configuration option to disable watch commands (`-W`)

### Contributors

The following people contributed to this release since 1.5.20.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.20..1.5.21` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.21

```
     4	David Carlier
     4	Kanak Kshetri
     3	dormando
     2	Carl Myers
     2	Daniel Schemmel
     2	Iqram Mahmud
     1	David J. M. Karlsen
     1	Josh Kupershmidt
     1	Martin Tzvetanov Grigorov
     1	Sailesh Mukil
     1	Tharanga Gamaethige
     1	iqr4m
     1	q66

```
