### Memcached 1.6.7 Release Notes

Date: 2020-9-4

### Download

http://www.memcached.org/files/memcached-1.6.7.tar.gz

### Overview

Mainly a bugfix release. `-o resp_obj_mem_limit` is deprecated, as the memory
is all pooled from `read_buf_mem_limit` now. Almost all connection memory is
now managed by this one tunable.

Fixes a bug preventing 1.6 series to work properly on OS X. Improves automated
slab rebalancing for bursty writes.

Contains code refactors and fixes related to future work. Should not have any
functional changes.

### Fixes

  * Dockerfile - allow override of config opts
  * Improve page balancing when writes are bursty
  * main: split binary protocol into `proto_bin.c`
  * main: split text protocol into `proto_text.c`
  * add openssl errors to SSL certificate loading error messages
  * skip setting the resource limits in debug builds
  * Use signal function instead of sigignore
  * fixing the basic tls test so it exits correctly when fails
  * net: remove most response obj cache related code
  * net: carve response buffers from read buffers
  * Do not join lru and slab maintainer threads if they do not exist
  * Restore SAN entries in testing TLS certificates
  * Changed code using strtol to use `safe_strtol` wrapper
  * Fix TCP failure under OS X.

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.6.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.6..1.6.7` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.7

```
     8	dormando
     2	Tharanga Gamaethige
     2	Tomas Korbar
     1	Guido Iaquinti
     1	Kevin Lin
     1	Mark Hagger
     1	minkikim89
     1	prudhvi

```
