### Memcached 1.5.20 Release Notes

Date: 2019-11-11

### Download

http://www.memcached.org/files/memcached-1.5.20.tar.gz

### Overview

Bugfix release.

Security issue: When enabling SASL authentication for binary protocol,
enabling UDP mode would allow bypassing SASL. Now refuses to start with both
UDP and SASL enabled. Text mode authentication was not vulnerable.

Includes a performance improvement for page rebalancing. Full discussion here:
https://github.com/memcached/memcached/pull/524 - in some circumstances, page
will be moved between slab classes much faster than before.

### Fixes

  * Security fix: Don't allow UDP with binprot SASL
  * Remove multiple double-initializations of condition variables and mutexes
  * Fix data race in `assoc_start_expand`
  * Use a proper data type for `settings.sig_hup`
  * restart: add error handling when a tag is not found in a metadata file.
  * doc: Update rfc2629.dtd, use local copy, fix error, and fix warning
  * doc: Fix out-of-tree build
  * slab rebalance performance improvements
  * fix potential deadlock bug in log watcher
  * Support running tests in out-of-tree build
  * configure: Fix cross-compilation errors
  * DTrace build fix

### New Features

None.

### Contributors

The following people contributed to this release since 1.5.19.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.19..1.5.20` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.20

```
     6	Ola Jeppsson
     3	Daniel Schemmel
     2	minkikim89
     1	Daniel Byrne
     1	David Carlier
     1	dormando
     1	neal-zhu

```
