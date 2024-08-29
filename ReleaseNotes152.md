### Memcached 1.5.2 Release Notes

Date: 2017-9-30

### Download

http://www.memcached.org/files/memcached-1.5.2.tar.gz

### Overview

Fixes a crash when storing more than 3.2 billion items in a single instance.

Bug is from 2006 :)

### Fixes

  * fix more binary protocol documentation errors.
  * fix segfault during 31b -> 32b hash table expand
  * don't create hashtables larger than 32bit
  * some non-user-facing code changes for supporting future features.

### New Features

None

### Contributors

The following people contributed to this release since 1.5.1.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.1..1.5.2` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.2

```
     7	dormando

```
