### Memcached 1.5.9 Release Notes

Date: 2018-7-7

### Download

https://www.memcached.org/files/memcached-1.5.9.tar.gz

### Overview

Bugfix release.

Important note: if using `--enable-seccomp`, privilege dropping is no longer
on by default. The feature is experimental and many users are reporting
hard to diagnose problems on varied platforms.

Seccomp is now marked EXPERIMENTAL, and must be explicitly enabled by adding
`-o drop_privileges`. Once we're more confident with the usability of the
feature, it will be enabled in `-o modern`, like any other new change. You
should only use it if you are willing to carefully test it, especially if
you're a vendor or distribution.

Also important is a crash fix in extstore when using the ASCII protocol, large
items, and running low on memory.

Thanks to the many people who've participated in this release :) Lots of
commits from the community this time around!

### Fixes

  * fix ASCII get error handling (+ extstore leak)
  * drop_privileges is no longer default if available.
  * remove bad assert from crawler
  * Mark seccomp experimental
  * Include keys with non-[\w.~-] bytes in memcached-tool dump
  * whitelist clock_gettime in seccomp rules
  * Fix segfault: Prevent calling sasl_server_step before sasl_server_start
  * fix flaky lru-maintainer test (OS X)

### New Features

  * support transparent hugepages on Linux (`-L` option)

### Contributors

The following people contributed to this release since 1.5.8.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.8..1.5.9` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.9

```
     5	dormando
     3	Paul Furtado
     2	Linkerist
     2	Peter (Stig) Edwards
     2	Vadim Pushtaev
     1	Charmander
     1	Chen-Yu Tsai
     1	Sjon Hortensius
     1	Stanisław Pitucha

```
