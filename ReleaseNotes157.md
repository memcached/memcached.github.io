### Memcached 1.5.7 Release Notes

Date: 2018-3-28

### Download

http://www.memcached.org/files/memcached-1.5.7.tar.gz

### Overview

Bugfix release. Many thanks to the mostly outside contributors for this one!

Fixes alignment issues for 64bit ARM processors, seccomp portability, and a
refcount leak with extstore while using binary touch commands. Full detail
below.

### Fixes

  * extstore: fix ref leak when using binary protocol with TOUCH,GAT,GATK
  * Drop supplementary groups in addition to setgid
  * Use HAVE_SASL_CB_GETCONFPATH
  * Fix SASL_CB_GETCONF(PATH) detection
  * Rewrite memchached-tool 'dump' method to use new lru_crawler interface.
  * Fixes decrement-before-check problem (issue #362).
  * document in manpage that port 0 is off.
  * Fix SIGBUS from alignment issues on 64bit ARM
  * Update seccomp with syscalls found on Arch
  * Enforce seccomp policy (kill process)
  * Support seccomp on musl
  * update --help for UDP default
  * Fix sed options order in rpm specfile


### New Features

None.

### Contributors

The following people contributed to this release since 1.5.6.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.6..1.5.7` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.7

```
     4	Stanisław Pitucha
     4	dormando
     3	Calin Iorgulescu
     2	Peter (Stig) Edwards
     1	Anthony Ryan
     1	Fangrui Song
     1	Josh Soref
     1	Ori Shalev
     1	Remi Collet

```
