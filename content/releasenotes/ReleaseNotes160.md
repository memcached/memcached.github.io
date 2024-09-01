### Memcached 1.6.0 Release Notes

Date: 2020-3-8

### Download

https://www.memcached.org/files/memcached-1.6.0.tar.gz

### Overview

1.6.0 brings significant new features and fixes to memcached.

The external flash storage (https://memcached.org/extstore) system is now compiled by
default. The system still requires some careful attention to run in a large
production, but it is generally considered stable. We'll continue to improve
the defaults incrementally. While it is compiled by default, it has to be
explicitly enabled at start time, so it does not affect installs unless you
intend to use it. It can be disabled at compile time via `--disable-extstore`

The new meta protocol
(https://github.com/memcached/memcached/wiki/MetaCommands and doc/protocol.txt
in your tarball) now has more features than the binary protocol. Anything you
can do with the text/binary protocol can be done with the new meta commands.
These commands can bring huge benefits to performance and correctness of cache
systems.

At the same time, the binary protocol is now officially deprecated. We will
support it and provide fixes for years to come, but it will not receive new
commands or updates. All users should use meta or text protocols.

Finally, the network code has been refactored to allow automatic batching of
response syscalls. IE: If you submit multiple get commands in the same TCP packet, memcached will tend to send the responses with a single syscall. Previously, especially for the binary protocol, all keys fetched would use separate syscalls for each response. The only time this wasn't true was for a text "multiget". See details in the MetaCommands wiki page for how to efficiently use pipelining.

You don't need to do much pipelining to see significant benefit: averaging 1.5
keys per syscall could reduce server CPU by up to 25% and latency by at least
a few percent.

With this change, many buffers that were statically assigned to connected
clients are now only used on-demand. This reduces the amount of memory usaged
by an idle client connection from a minimum of 4.5 kilobytes to 400-500 bytes
or so. It also removes many inline mallocs, reallocs, frees, and so on that
could cause memory fragmentation for systems with a huge number of
connections.

Every worker thread manages its own pool of read and write buffers for active
client connections. This can be limited by two new tunables, though most users
should never change these values.

`-o resp_obj_mem_limit=N` and `-o read_buf_mem_limt=N` - N is a decimal amount
of megabytes.

This can be useful if you have, say, 500,000 connected clients but don't want
to instantly OOM your system if a network hiccup causes many of them to be
unable to completely read a new request or completely flush responses. When
the memory limit is reached connections will be closed.

Many thanks to Netflix and all the other contributors in this release. A lot
of these features have been baking for a long time.

We believe this software to be of high quality. However this release is a bit
larger than usual. If you have critical infrastructure on memcached, please
roll this out slowly if possible.

### Fixes

  * meta: indicate refcount overflow
  * meta: fix refleak in mget
  * fix: all new connections were counted as rejected
  * timedrun: proper signal handler initialization.
  * restart: fix potential double free
  * `stats_prefix`: fix test failure due to non-determinism
  * fix bug where sasl will load config the wrong path
  * meta: make return codes more generic
  * config.h for util.c, fix htonll comp. failure
  * hash: fix build failure against gcc-10
  * fix memory leaks in unit tests
  * fix make order in BUILD instructions

### New Features

  * extstore: enable by default.
  * meta: arithmetic command (`ma`) for incr/decr
  * network: transient static read buffer for conns
  * network: response stacking for all commands

### Contributors

The following people contributed to this release since 1.5.22.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.22..1.6.0` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.0

```
    20	dormando
     3	David Carlier
     1	Kanak Kshetri
     1	Sergei Trofimovich
     1	Zheng Gu

```
