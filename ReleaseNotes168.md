### Memcached 1.6.8 Release Notes

Date: 2020-10-26

### Download

http://www.memcached.org/files/memcached-1.6.8.tar.gz

### Overview

Small security related release. A remote crash is possible if UDP is enabled.
The remediation is to upgrade or disable UDP. The crash was introduced in the
1.6 series.

Thanks to @Zaffy and @m6w6 for their reports.

UDP has not been enabled by default for years and it has no authentication or
security, so it most users should not have a high exposure to this bug.

This also includes two minor fixes.

### Fixes

  * Improve opening of authentication file
  * Fix over-freeing in internal object cache
  * udp: crash fix when receiving multi-packet uploads

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.7.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.7..1.6.8` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.8

```
     1	Tomas Korbar
     1	bitground
     1	dormando

```
