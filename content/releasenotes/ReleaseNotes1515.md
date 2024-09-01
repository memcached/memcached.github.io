### Memcached 1.5.15 Release Notes

Date: 2019-5-20

### Download

http://www.memcached.org/files/memcached-1.5.15.tar.gz

### Overview

A couple small bugfixes, and two notable changes. An authentication mode for
ASCII protocol, and removal of a minor feature flag.

`-o inline_ascii_response` was a compatability mode which was disabled since
1.5.0; it uses 10-20 more bytes of memory to speed up ASCII responses. Since
we switched from snprintf to a fast itoa implementation years ago it was disabled by
default. Now the option is gone.

Binary protocol was also not honoring the idle-timeout feature. This should
now be fixed.

### Fixes

  * Speed up incr/decr by replacing snprintf.
  * Use correct buffer size for internal URI encoding.
  * change some links from http to https
  * Fix small memory leak in testapp.c.
  * free window_global in slab_automove_extstore.c
  * remove inline_ascii_response option
  * -Y [filename] for ascii authentication mode
  * fix: idle-timeout wasn't compatible with binprot

### New Features

`-Y [authfile]` enables an authentication mode for ASCII protocol. See
doc/protocol.txt for the most up to date information. This feature is
experimental; there's a small chance it could change based on community
feedback. Unlike the SASL authentication for binary protocol, this is built in
by default and has no external dependencies. It is also far simpler to use; we
get a constant slow trickle of support requests around SASL authentication.

When enabled, UDP and binary protocols are also disabled.

The files take up to 8 user:pass tokens:

```
foo:bar
baz:quux
```

The feature isn't meant to give a fine grained set of access to a large number
of users: there's currently no further restriction once authenticated.
Multiple tokens are useful for token rotation or similar, where old tokens
should work alongside newer ones for a while.

It's used via the set command:

```
set key 0 0 N\r\n
auth pass\r\n
```

where N is the length of the "auth pass\r\n" payload.

The "key" value is ignored. When used in a cluster, it may not be easy to
ensure an authentication request hits a specific server. For those cases, a
failed command may retry using the same key, but using a SET command with a
supplied "auth pass" token. An AUTH specific command may be added in the future, as well.

Reloading the auth file is done by sending a HUP signal to the memcached
process. We may also add a management command to reload the file.

If you are a client author and have feedback about this feature, please hit up
the mailing list and let us know. Thanks!

Note: worth mentioning this doesn't protect against timing attacks. We're not
against extra security features, but need to understand the use case better
and add just what's needed. If you need better security, the TLS feature is
better.

### Contributors

The following people contributed to this release since 1.5.14.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.14..1.5.15` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.15

```
     4	dormando
     2	Tharanga Gamaethige
     1	David Carlier
     1	kun
     1	liwenlong05

```
