### Memcached 1.6.10 Release Notes

Date: 2021-7-25

### Download

http://www.memcached.org/files/memcached-1.6.10.tar.gz

### Overview

Meta protocol is no longer marked as EXPERIMENTAL. A breaking protocol change
should only happen in very extreme situations (security, major usage fault),
which we do not expect to happen. Any further changes to the meta protocol
will be the addition of new commands or flags to existing commands.

See doc/protocol.txt for a description of the meta protocol.
https://github.com/memcached/memcached/wiki/MetaCommands wiki page for
examples.

The XXH3 hash has been added to the list of hash table algorithms, but not
enabled by default. There is a measurable performance improvement for long
keys (> 20 characters), but overall the difference is small.

Release otherwise contains bugfixes.

### Fixes

  * Fix minor severity heap buffer overflow reading `--auth-file`
  * `stats_prefix.c`: Check for NDEBUG before using `total_written` variable
  * Add settings stat for `shutdown_command` enabled
  * [docker] Add user and expose the memcached port
  * meta: remove EXPERIMENTAL mark + doc fixes
  * meta: response code OK -> HD
  * meta: fix metaset syntax
  * meta: repairs to mset command
  * hash: add XXH3 to list of hash algorithms.
  * fix arm64 crc32 on old glibc/gcc.
  * extstore: fix crash on 'stats extstore'
  * seccomp: extend allowed rules for extended usage
  * The total number of UDP datagrams required for the message is calculated incorrectly.
  * meta: allow base64'ed binary keys with 'b' flag
  * small improvements to readme
  * Added debugtime command for test suite

### New Features

The meta protocol can now transmit binary encoded keys by encoding the key in
base64 and sending a 'b' flag with the command. If for example your key
composes of several large numerics (userids and so on), these can be encoded
directly as binary. This shortens the key which can save some memory on the
server. See `doc/protocol.txt` for more details.

### Contributors

The following people contributed to this release since 1.6.9.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.9..1.6.10` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.10

```
    13	dormando
     2	minkikim89
     1	Brian Aker
     1	Hervé Beraud
     1	Kevin Lin
     1	Khem Raj
     1	LSmithx2
     1	Levente Polyak
     1	Tomas Korbar
     1	Tyson Andre
     1	tom

```
