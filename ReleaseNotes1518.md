### Memcached 1.5.18 Release Notes

Date: 2019-9-17

### Download

http://www.memcached.org/files/memcached-1.5.18.tar.gz

### Overview

Big new feature: warm cache restart!

### Fixes

None.

### New Features

Memcached 1.5.18 and newer can recover its cache between restarts. It can restart after upgrades of the
binary, most changes in settings, and so on. It now also supports
using persistent memory via DAX filesystem mounts. See below for more details.

Use it by adding: `-e /tmpfs_mount/memory_file` to your startup options.

`/tmpfs_mount/` must be a ram disk of some sort, big enough to satisfy the
memory limit specified on startup with `-m`. To gracefully restart; send a
SIGUSR1 signal to the daemon, and wait for it to shut down and exit.

It will create a `/tmpfs_mount/memory_file.meta` file on shutdown. On restart
it will read this file and ensure the restart is compatible. If it is not
compatible or the file is corrupt, it will start with a clean cache.

If you change some parameters the cache will come up clean:

- The memory limit (`-m`)
- The max item size.
- Slab chunk sizes.
- Whether CAS is enabled or not.
- Whether slab reassignment is allowed or not.

... you are able to change all other options inbetween restarts!

Important Caveats (see below for more detail):

- System clock must be set correctly and must not jump while memcached
  is down.
- Deletes, sets, adds, incr/decr/etc commands will be missed while instance
  restarts!
- The earliest version you can restart from is 1.5.18. Older versions cannot
  upgrade to 1.5.18 without losing the cache.

Consider this feature experimental for the next few releases, but please give
it a try and let us know what you think.

### DETAILS

This works by putting memory related to item data into an external
mmap file (specified via `-e`). All other memory: the hash table, connection
memory, etc, stay in main RAM. When the daemon is restarted, it runs a pass
over the item data and fixes up the internal pointers and regenerates
the hash table. This typically takes a few seconds, but if you have close to a
billion items in memory it can take two or three minutes.

Once restarted, there is no performance difference between restartable and
non-restartable modes.

#### DAX mounts and persistent memory

If you have a persistent memory device, you can utilize this feature to extend
memcached's memory into persistent memory. This does _not_ make
memcached crash safe! It will put item memory into your persistent memory
mount, while the rest of memory (hash table/buffers/etc) use system DRAM. This
is a very high performance mode as the majority of memory accesses stay in DRAM. Also, with a graceful shutdown memcached can be
restarted after reboots so long as the DAX mount persists.

See your persistent memory vendor's documentation for how to configure a DAX
mount.

You can see extensive testing we did in this and other modes on our blog: https://memcached.org/blog/persistent-memory/

#### CAVEATS

Your system clock _must_ be set correctly, or at least it must not move before
memcached has restarted. The only way to tell how much time has passed while
the binary was dead, is to check the system clock. If the clock jumps forward
or backwards it could impact items with a specific TTL.

Users must keep in mind that while an instance is stopped, it may be missing
updates from the network; deletes, sets, and so on. It is only safe to restart
memcached if your architecture can handle this by either pausing/buffering
updates, or restarting at a time when no changes are happening to the cache.

#### FUTURE WORK

This feature is presently incompatible with extstore. This should be resolved
in the next version or two. Further additions to usability, safety, etc, will
be added based on feedback we receive.


### Contributors

The following people contributed to this release since 1.5.17.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.17..1.5.18` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.18

```
     2	dormando

```
