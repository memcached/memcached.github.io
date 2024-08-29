### Memcached 1.5.3 Release Notes

Date: 2017-11-4

### Download

http://www.memcached.org/files/memcached-1.5.3.tar.gz

### Overview

Bugfix release. Adds "GAT" command from binprot to asciiprot.

### Fixes

  * Add warning about time on very low TTL's in doc/protocol.txt
  * pledge privdropping support for OpenBSD
  * make for loop more clear in logger watcher
  * fix theoretical leak in process_bin_stat
  * fix use of unitialized array in lru_maintainer
  * -o no_hashexpand to disable hash table expansion
  * fix chunked items set in binprot, read from ascii


### New Features

  * adds get and touch command for ascii protocol

"gat [exptime] key [key...]\r\n" will fetch the item and refresh its
expiration time. This is great for keys you'd normally either manually delete,
or set to 0 TTL. Items rarely hit will expire, leaving more room for others.

This command has existed in the binary protocol for a long time, but now
there's party for ascii users.

### Contributors

The following people contributed to this release since 1.5.2.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.2..1.5.3` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.3

```
     6	dormando
     1	Giovanni Bechis
     1	Theo Najim

```
