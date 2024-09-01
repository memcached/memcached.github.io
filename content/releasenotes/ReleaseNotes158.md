### Memcached 1.5.8 Release Notes

Date: 2018-5-25

### Download

http://www.memcached.org/files/memcached-1.5.8.tar.gz

### Overview

Number of important bugfixes for seccomp and extstore. Extstore platform
portability has been greatly improved for ARM and 32bit systems.

### Fixes

  * fix sasl tests
  * fix flaky extstore tests
  * alignment and 32bit fixes for extstore
  * crc32c for aarch64 support
  * fix rare partial deadlock during hash table expansion
  * Add Dockerfile definitions
  * Fix lru-crawler behaviour for seccomp
  * Fail loudly if seccomp setup fails

### New Features

Thanks to @aparida1 from Qualcomm, extstore now has hardware CRC32 support for
ARMv8 when available.

Enabling it requires a few extra steps:
`./configure CFLAGS="-march=armv8-a+crc" --enable-extstore --enable-arm-crc`

this should autodetect better in future releases.

Thanks to the help of @tianon and a few bug reports, extstore now works on
32bit systems and a number of ARM platforms.

More information on extstore can be found here:
https://github.com/memcached/memcached/wiki/Extstore

### Contributors

The following people contributed to this release since 1.5.7.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.7..1.5.8` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.8

```
     8	dormando
     3	Stanisław Pitucha

```
