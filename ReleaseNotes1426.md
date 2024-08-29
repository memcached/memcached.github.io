### Memcached 1.4.26 Release Notes

Date: 2016-6-17

### Download

https://www.memcached.org/files/memcached-1.4.26.tar.gz

### Overview

The usual routine of small fixes comes with the first pass of a big new
feature: on-demand live logging! If you've ever reached for `stats cachedump`,
you'll be much better served by this feature. See below for details.

### Fixes

  * bump some global stats to 64bit uints (total_connections)
  * sanity check for maximum connections setting
  * get_expired stats
  * make cachedump print 0exp for noexpire items
  * compilation fixes for GNU/Hurd
  * fix building of binary documentation files.
  * document negative expiration values
  * fix build with musl libc

### New Features

  * There is now a `-o modern` option which combines all of the new
    recommended features.

In the previous release, it was recommended that memcached be started with:

`-o slab_reassign,slab_automove,lru_crawler,lru_maintainer,maxconns_fast,hash_algorithm=murmur3`

... to get the benefit of new features. These will become the default soon. In
the meantime a shorthand of `-o modern` can be used which will act like the
above line, and include new recommended defaults ahead of them becoming actual
defaults. Memcached has always been treated extremely conservatively so this
tries to be as safe as possible.

#### Logging!

```
$ telnet localhost 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
watch fetchers
OK
```

Connect again:
```
$ telnet localhost 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
get foo
END
```

First connection will output:
```
ts=1466120156.99667 gid=1 type=item_get key=foo status=not_found
```
`watch evictions` can show details about evicted items:
```
ts=1466120156.304163 gid=57180 type=eviction key=n%2Cfoo1539 fetch=no ttl=-1
la=0
```
(ttl: time remaining before expiration. la: last access time. fetch: whether
or not it was ever read)

In this release `fetchers` `mutations` and `evictions` are supported. You can
mix and match on a connection:

`watch fetchers mutations evictions`

Logs will continue to emit until the watcher disconnects. If the watcher gets
behind on reading data, log lines will be dropped (and a notice appended when
it resumes).

 * Logs are collected asynchronously. A background thread renders and writes
   to clients. This should reduce the latency and max CPU overhead of using
   the logs. If no watchers are active, logs aren't generated at all.

 * Logs are potentially received out of order. Re-order by the `gid=` option
   if this is important.

 * Keys are represented by "URI Encoding". Most ASCII keys are unaffected, but
   some characters will be replaced by `%NN` equivalents.

 * The initial fetchers/mutations code are monitoring *internal* key fetches
   and stores. This means a "set" command runs first a get and then a store,
   showing two log lines. A multiget will emit one log line per key.

 * Further code cleanups and more logging endpoints are scheduled for future
   releases. There's also a potential for modular log formats. Let us know
   what's important for you!

See the [pull request](https://github.com/memcached/memcached/pull/127) for
full detail.

### Contributors

The following people contributed to this release since 1.4.25.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.25..1.4.26` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.26

```
    23	dormando
     4	Guillaume Delacour
     3	sergiocarlos
     2	祁冰
     1	Caleb Shay
     1	Matt Fowles Kulukundis
     1	Natanael Copa
     1	hiracy
     1	meteorgan

```
