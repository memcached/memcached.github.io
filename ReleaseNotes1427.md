### Memcached 1.4.27 Release Notes

Date: 2016-6-24

### Download

http://www.memcached.org/files/memcached-1.4.27.tar.gz

### Overview

Fix some compile and flaky test issues with 1.4.26. Adds several new features
and fixes.

### Fixes

  * treat and print item flags as unsigned int
  * make watcher test less flaky.
  * indent fixes in thread_libevent_process to make future changes easier
  * fix solaris compiler warning in bipbuffer.c
  * add #include <stdio.h> to fix error: implicit declaration of function ‘snprintf’ on centos
  * change LRU crawler fprintf to use %u format instead of %d

  * add -o watcher_logbuf_size=N worker_logbuf_size=N

You don't need this last one unless you're writing tests or need to carefully
tune the logger to avoid skipped or dropped lines.

### New Features

  * memcached idle connection killer
    (https://github.com/memcached/memcached/pull/80)

If specifying `-o idle_timeout=s` where s is seconds, connected clients idle
for at least that long will be asked to close. Defaulted to off, but could be
useful if you tend to have clients that go out to lunch and never come back.

Thanks to Jay Grizzard for this feature!

  * `cache_memlimit` command for tuning runtime maxbytes
    (https://github.com/memcached/memcached/pull/170)

Finally! You can now dynamically increase the amount of memory available to
memcached without having to restart it. If `-o modern` is in use (slab
rebalancer enabled), it can be told to free memory back to the system by
lowering the memory limit.

A big caveat is that it's up to the OS as to if the memory can be returned to 
the OS, or if it stays in the application heap. Do careful testing before
relying on being able to free memory.

  * online hang-free "stats sizes" command.
  * allow manually specifying slab class sizes
    (https://github.com/memcached/memcached/pull/169)

These two features are part of the one pull request. The `stats sizes` command
would hang one of the worker threads while iterating all of your items.

Now, the process is dynamic, tallying sizes as items are added to or removed
from the cache. It must be enabled at starttime via `-o track_sizes`, or
dynamically via `stats sizes_enable` and `stats sizes_disable` ascii commands.

If you have item sizes that are very static, or align very poorly by using the
`-f` feature, you can now specify the exact sizes of the slab classes via
commands like `-o slab_sizes=100-200-300-400-500`.

### Contributors

The following people contributed to this release since 1.4.26.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.26..1.4.27` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.27

```
     9	dormando
     2	Jay Grizzard
     1	Mathieu CARBONNEAUX
     1	Ryan McCullagh

```
