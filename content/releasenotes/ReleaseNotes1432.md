### Memcached 1.4.32 Release Notes

Date: 2016-10-12

### Download

http://www.memcached.org/files/memcached-1.4.32.tar.gz

### Overview

Bugfix release. One important bug introduced in 1.4.29 could cause OOM errors
if you have many items with keys longer than the data.

### Fixes

  * fix missing evicted_time display in stats output
  * Update old ChangeLog note to visit Github wiki
  * fix OOM errors with newer LRU
  * Misc typo fixes

### New Features

None this time!

### Contributors

The following people contributed to this release since 1.4.31.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.31..1.4.32` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.32

```
     4	dormando
     1	Adam Chainz
     1	Giovanni Bechis
     1	akisssa

```
