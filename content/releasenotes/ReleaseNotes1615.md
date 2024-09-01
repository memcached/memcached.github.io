### Memcached 1.6.15 Release Notes

Date: 2022-3-29

### Download

http://www.memcached.org/files/memcached-1.6.15.tar.gz

### Overview

No mainline changes vs 1.6.14. This release is a roll-up of fixes to the
experimental proxy mode.

### Fixes

  * proxy: Fix buffer overflow and prevent recv() of 0 byte
  * proxy: allow await() to be called recursively
  * proxy: mcp.request(cmd, [val | resp])
  * proxy: hacky method of supporting noreply/quiet
  * proxy: add `ring_hash` builtin
  * proxy: fix logger entry memory corruption
  * storage: parameterize the compaction thread sleep
  * proxy: pull chunks into individual c files
  * proxy: documentation updates
  * proxy: "stats settings" for proxy
  * proxy: await improvements
  * proxy: trivial support for `SO_KEEPALIVE` on backend
  * mcmc: upstream update for `SO_KEEPALIVE`
  * proxy: fix crash on `stats proxy` sans user stats
  * proxy: enable `backend_total` stat
  * proxy: track in-flight requests
  * proxy: add some basic logging for backend errors
  * proxy: logging improvements + lua mcp.log()
  * proxy: add stats for commands seen


### New Features

None.

### Contributors

The following people contributed to this release since 1.6.14.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.14..1.6.15` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.15

```
    18	dormando
     1	Fei Hu

```
