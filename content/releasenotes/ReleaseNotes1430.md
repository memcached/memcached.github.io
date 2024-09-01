### Memcached 1.4.30 Release Notes

Date: 2016-8-11

### Download

http://www.memcached.org/files/memcached-1.4.30.tar.gz

### Overview

Bugfix release, with a critical fix to large item support.

### Fixes

  * Add MemoryDenyWriteExecute to the systemd service
  * Handle end of line comment on memcached.conf
  * Add missing parameters and escape hypens as minus to manpage
  * Modernize unit file in systemd
  * crawler now uses rate limiter sleeps properly (CPU overusage)
  * add slab_chunk_max to `stats settings`

Importantly:

  * fix over-allocating with large item support.

If you set `-I 2m`, memcached was allocating 2 megabytes of memory per page,
then only using 1mb. This would lead to it hitting the malloc limit with only
50% of the memory available before. This gets worse the more distant `-I` is
from 1MB.

If you are still seeing issues with memory efficiency with large item support
(`-I` set higher than 1m default), try the startup setting: `-o slab_chunk_max=524288`
Most workloads will function fine, and it should nearly always be better than
how memory efficiency worked prior to the new feature (when using large
items).

### New Features


### Contributors

The following people contributed to this release since 1.4.29.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.29..1.4.30` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.30

```
     6	dormando
     2	Guillaume Delacour
     1	Craig Andrews
     1	Remi Collet

```
