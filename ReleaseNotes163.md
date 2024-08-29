### Memcached 1.6.3 Release Notes

Date: 2020-3-28

### Download

http://www.memcached.org/files/memcached-1.6.3.tar.gz

### Overview

Bugfix release plus some code cleanup. Includes a fix for another remote crash,
this time in the new metaget code.

### Fixes

  * crash fix: errstr wasn't initialized in metaget
  * fix startup segfault for low conns + idle thread
  * restart: fix corrupted restart in some scenarios
  * restart: always wipe memory from global pool
  * restart: fix rare segfault on shutdown
  * tls: fix crash with `refresh_certs` command when TLS is disabled

### New Features

`-o ssl_session_cache` can be used (when TLS is compiled in) to enable server
side session reuse.

`./configure --enable-static` for static builds

### Contributors

The following people contributed to this release since 1.6.2.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.2..1.6.3` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.3

```
    10	dormando
     2	Kevin Lin

```
