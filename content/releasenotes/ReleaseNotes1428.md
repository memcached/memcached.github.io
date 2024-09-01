### Memcached 1.4.28 Release Notes

Date: 2016-7-1

### Download

http://www.memcached.org/files/memcached-1.4.28.tar.gz

### Overview

Bugfix release. Thanks to everyone who tested!

While good progress has been made on a new feature, it's getting some more
burn-in time and this fix/cleanup release is happening in the meantime.

### Fixes

  * systemd init script hardening hardening
  * make watcher.t be even less flaky.
  * don't fail on systems without 64bit atomics
  * small code refactor of logger
  * clean up global stats code a little.
  * add get_flushed counter, fix expired_unfetched
  * Fix undefined behavior in vlen check


### New Features

None. Several new features have been added in 1.4.26 and 1.4.27, please refer
to those release notes.

### Contributors

The following people contributed to this release since 1.4.27.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.27..1.4.28` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.28

```
     7	dormando
     2	Craig Andrews
     1	J. Grizzard
     1	Paul Lindner

```
