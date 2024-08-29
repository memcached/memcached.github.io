### Memcached 1.4.33 Release Notes

Date: 2016-10-31

### Download

http://www.memcached.org/files/memcached-1.4.33.tar.gz

### Overview

Serious remote code execution bugs are fixed in this release.

The bugs are related to the binary protocol as well as SASL authentication of the
binary protocol.

If you do not use the binary protocol at all, a workaround is to start
memcached with `-B ascii` - otherwise you will need the patch in this release.

The diff may apply cleanly to older versions as the affected code has not
changed in a long time.

Full details of the issue may be found here:
http://blog.talosintel.com/2016/10/memcached-vulnerabilities.html

### Fixes

  * CVE reported by cisco talos

### New Features

  * None.

### Contributors

The following people contributed to this release since 1.4.32.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.32..1.4.33` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.33

```
     1	dormando

```
