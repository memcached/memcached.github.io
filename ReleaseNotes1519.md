### Memcached 1.5.19 Release Notes

Date: 2019-9-30

### Download

http://www.memcached.org/files/memcached-1.5.19.tar.gz

### Overview

This release contains an experimental new protocol extension for the default
Text Protocol. This extension includes a flexible set of new commands
encompassing features previously unique to the binary protocol, as well as
many new updates allowing a reduction of network roundtrips for advanced
features.

For details, please see the "Meta Commands" section in doc/protocol.txt
https://github.com/memcached/memcached/blob/master/doc/protocol.txt

For use case examples, see the wiki:
https://github.com/memcached/memcached/wiki/MetaCommands

These commands are _experimental_ until stated otherwise in a future release.
This means the syntax _may_ change based on community feedback. We feel this
is unlikely, but early adopters should keep track of the release notes. We
don't expect this process to take more than a few releases. After a short
period we will no longer consider incompatible changes.

This release also contains a fix for the restart code and a memory leak in the
TLS code when connections fail to properly establish. Also some other minor
updates and fixes.

### Fixes

  * Keep "last access" time up to date in default segmented LRU mode
  * slow down t/watcher.t test more
  * Add include stdio.h to restart.c
  * Reload CA cert in refresh routine
  * TLS: fix leak of SSL context on accept failure
  * drop privileges for FreeBSD.
  * Make memcached setgroups failure non-fatal
  * restart: use /tmp for restart tests.
  * restart: add missing msync at close time


### New Features

The meta commands feature, as noted above.

### Contributors

The following people contributed to this release since 1.5.18.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.18..1.5.19` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.19

```
    10	dormando
     1	David Carlier
     1	Kevin Lin
     1	Piotr Balcer
     1	jwbee
     1	minkikim89

```
