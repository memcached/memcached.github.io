### Memcached 1.6.9 Release Notes

Date: 2020-11-20

### Download

http://www.memcached.org/files/memcached-1.6.9.tar.gz

### Overview

This change has a few useful new features, along with a lot of bugfixes and
some internal code reorganization for future projects. We have been questing
to improve test quality and stamp down bugs that came in over the 1.6 series.

As usual many thanks to the numerous contributors who sent in patches or
helped test this release!

### Fixes

  * crawler: remove bad mutex unlock during error
  * `idle_timeout`: avoid long hangs during shutdown
  * extstore: use fcntl locking on disk file
  * portability fix for getsubopt
  * illumos build fixes + require libevent2
  * core: generalize extstore's defered IO queue
  * fix connection limit tests
  * logger: fix spurious watcher hangups
  * watcher.t: reduce flakiness
  * Extend test CA validity to 500 years
  * adjust "t/idle-timeout.t" be more forgiving

### New Features

  * arm64: Re-add arm crc32c hw acceleration for extstore
  * restart mode: expose `memory_file` path in `stats settings`
  * 'shutdown graceful' command for raising SIGUSR1
  * Introduce NAPI ID based worker thread selection (see `doc/napi_ids.txt`)

  * item crawler hash table walk mode

The background item crawler thread is used by default to walk LRU's and
actively reclaim expired items. It can also be used by end users to examine
the cache via the `lru_crawler metadump` command.

The metadump command can be told to walk specific LRU's, in case you are
curious what is taking up memory in lower or higher slab classes. On the
downside LRU's will naturally reorder as things happen. There is also an issue
where items that are very frequently accessed are invisible to the LRU
crawler.

New with this release, if you invoke the crawler via: `lru_crawler metadump
hash`, the crawler will instead visit each bucket in the hash table. This will
ensure each item is visited once, but the search cannot be limited in the same
way.

Note this does not in any way snapshot memory. If items are deleted or added
to the hash table after the walk starts, they may or may not be seen.

### Contributors

The following people contributed to this release since 1.6.8.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.8..1.6.9` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.9

```
    15	dormando
     2	Kevin Lin
     1	Ali Saidi
     1	Bernhard M. Wiedemann
     1	David Carlier
     1	Kleber
     1	Sridhar Samudrala
     1	Tianon Gravi
     1	pkarumanchi9

```
