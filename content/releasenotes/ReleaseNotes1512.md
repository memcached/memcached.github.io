### Memcached 1.5.12 Release Notes

Date: 2018-11-3

### Download

http://www.memcached.org/files/memcached-1.5.12.tar.gz

### Overview

Single bugfix release. Fixes a refcount leak that could happen when attempting
to run incr/decr against a:

 * 0 byte value
 * CHUNKED (> 512k) item
 * extstore item that has been flushed to disk

### Fixes

  * fix INCR/DECR refcount leak for invalid items


### New Features

None

### Contributors

The following people contributed to this release since 1.5.11.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.11..1.5.12` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.12

```
     1	dormando

```
