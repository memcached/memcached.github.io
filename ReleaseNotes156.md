### Memcached 1.5.6 Release Notes

Date: 2018-2-27

### Download

http://www.memcached.org/files/memcached-1.5.6.tar.gz

### Overview

This is a bugfix release, but it primarily disables the UDP protocol by default.

In the last few days reports of UDP amplification attacks utilizing inesure
memcached instances have surfaced. Attackers are able to set large values into
memcached, then send requests via spoofed UDP packets. Memcached will then
send a very large number of very large UDP packets back in response.

12 years ago, the UDP version of the protocol had more widespread use: TCP
overhead could be very high. In the last few years, I've not heard of anyone
using UDP anymore. Proxies and special clients allow connection reuse, which
lowers the overhead. Also, RAM values are so large that TCP buffers just don't
add up as much as they used to.

That said, I don't have any way of knowing how many UDP installations there
are. Everyone who uses UDP and upgrades past this version, will find the UDP
protocol disabled unless they explicitly enable it via `-U 11211`. Hopefully
this one-time pain is acceptable.

Thanks for everyone who reached out in the last couple days to help understand
the problem and coordinate patches sent to linux and BSD distro's.

### Fixes

  * disable UDP port by default
  * systemd instancing support & rpm build improvements
  * fix gcc warnings in beta GCC
  * fix build with clang
  * fix for dtrace compilation on freebsd

### New Features

The RPM specfiles have been greatly improved for systemd installations, see:
https://github.com/memcached/memcached/commit/7141922a6188b00bc542b29c578506e0db52c9c7
for full detail.

### Contributors

The following people contributed to this release since 1.5.5.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.5..1.5.6` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.6

```
     4	dormando
     1	David Carlier
     1	J. Grizzard
     1	Manish Katiyar
     1	Miroslav Lichvar
     1	Qian Li

```
