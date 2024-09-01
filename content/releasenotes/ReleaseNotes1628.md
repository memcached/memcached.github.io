### Memcached 1.6.28 Release Notes

Date: 2024-5-31

### Download

https://www.memcached.org/files/memcached-1.6.28.tar.gz

### Overview

Fixes unfortunate potentially critical use-after-free bug in the proxy mode
that was introduced in 1.6.27. The bug is difficult to trigger but if you are
on 1.6.27 upgrading is strongly recommended.

Also adds experimental support for TLS to proxy backends. Please let us know
if you intend to use this feature as it has only received limited testing; we
will prioritize further work if folks are interested in using it.

### Fixes

  * core: Reorder r/w for itemstats aggregation
  * proxy: make iov limit bugs easier to see
  * proxy: fix stupid write flush bug
  * crawler: don't block during hash expansion
  * proxy: fix refcount leak in mcp.internal()

### New Features

  * memcached-tool: add -u flag to unescape special chars in keys names
  * proxy: add counters for VM memory and GC runs

Under `stats proxy`:
`vm_gc_runs`: increased each time a lua VM GC runs in any worker thread
`vm_memory_kb`: mostly current total memory usage of worker lua VM's in
  kilobytes.


  * proxy: backend TLS support [EXPERIMENTAL]
  * proxy: support to-be-closed for result objects
  * proxy: `res:close()`

If issuing get requests from `mcp.internal`, and you are not returning the
result object to the client, users should mark the result object as `<close>`
or manually call `res:close()` to release the reference on the item data.

  * proxy: add `mcp.backend_depth_limit(int)`

If a backend has a request queue depth higher than this limit, further
requests will fast fail.

### Contributors

The following people contributed to this release since 1.6.27.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.27..1.6.28` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.28

```
     9	dormando
     1	Daniel Vasquez-Lopez
     1	Pierre-Yves Rofes

```
