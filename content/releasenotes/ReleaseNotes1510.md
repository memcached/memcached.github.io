### Memcached 1.5.10 Release Notes

Date: 2018-8-10

### Download

http://www.memcached.org/files/memcached-1.5.10.tar.gz

### Overview

Build, Bugfixes, and ... Blocks?

NOTE: For extstore users, `-o ext_page_count=` is deprecated and no longer works.

To specify device size: `-o ext_path=/d/m/e:500G` - extstore figures out the
page count based on your desired page size. M|G|T|P supported.

This change is disruptive, but likely the last time I'll do this. There is
higher confidence in extstore every month and people should be ready to rely
on it.
If the arguments change in the future, they will follow a strict deprecation
cycle.

Also, basic JBOD support for extstore is here! If you have multiple
similar devices, simply specify `ext_path` multiple times and it will stripe
data across them, example:
`-o ext_path=/d1/e:1T,ext_path=/d2/e:1T`

Make sure you experiment and specify enough `ext_threads` to fully utilize the
drives. There's no hard rule on how many IO threads are necessary, but you
need fewer threads for devices with lower latency.

Future releases will allow mixing different types of devices, ie:

  * drive A is a small persistent memory (Optane or similar) device
  * drive B is a large inexpensive flash SSD

You will be able to direct "compacted", longer lived items, onto the SSD. Or
drive low TTL items onto the persistent memory, etc. This has been tested,
but requires adjustments to the compaction algorithm before going into
mainline.

### Fixes

  * fix alignment issues on some ARM platforms for chunked items
  * add missing va_end() call to logger_log()

### New Features

  * basic extstore JBOD support (noted in Overview)

  * split storage writer into its own thread

The extstore storage writer was co-located with the LRU maintenance thread.
This change separates it into its own thread, which brings up the performance
consistency and throughput. This is especially true during benchmarks, which
could easily starve the extstore flush routine of resources. Most users are
unlikely to notice a difference.

### Contributors

The following people contributed to this release since 1.5.9.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.9..1.5.10` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.10

```
     4	dormando
     1	Gleicon Moraes
     1	Miroslav Lichvar
     1	phantom9999

```
