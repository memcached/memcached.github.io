### Memcached 1.5.13 Release Notes

Date: 2019-4-15

### Download

https://www.memcached.org/files/memcached-1.5.13.tar.gz

### Overview

Experimental TLS support. Brought to you by Netflix, thanks! :)

See `memcached --help` for usage. Allows mixing TLS and non-TLS listeners (for
local monitoring, tests, etc). Has been reviewed and tested but please treat
it as experimental. Let us know if you run into any issues!

Requires a minimum OpenSSL version of 1.1.0. OpenSSL's API changed the API,
deprecating some old functions. They also redid the locking code entirely,
which scales much better. Old versions should be avoided for security reasons,
as well as for not giving good performance in multithreaded servers such as
memcached.

See doc/tls.txt for more information.

As of this writing no known memcached clients support TLS; please contact the
maintainers/authors of your client software to enquire about adding TLS
support. You can also use proxies, routers, and so on.

If you have extremely sensitive performance requirements, take care in
deploying this feature.

### Fixes

  * Basic implementation of TLS for memcached.
  * Improve Get And Touch documentation

### New Features

TLS support!

### Contributors

The following people contributed to this release since 1.5.12.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.12..1.5.13` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.13

```
     1	Andre Azevedo Pinto
     1	Tharanga Gamaethige

```
