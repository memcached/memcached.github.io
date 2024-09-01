### Memcached 1.5.4 Release Notes

Date: 2017-12-20

### Download

http://www.memcached.org/files/memcached-1.5.4.tar.gz

### Overview

New external storage (flash) shim. Still experimental and a little hard to
tune, but generally stable and well tested.

This new feature requires `./configure --enable-extstore` to be compiled in.
building it in might have a very minor performance impact even without using
the new shim. Netflix has been running production tests using this new system.

Without compiling in extstore, almost no new changes since 1.5.3.

### Fixes

  * make -I argument less position dependent
  * external storage base commit
  * lru_crawler metadump output ends with "END\r\n"
  * fix: -o no_lru_crawler didn't work

### New Features

extstore, a cleverly named external storage shim, is an addon for using flash
drives to expand cache storage.

For more detail, see the [Extstore](/Extstore) page. If this page is low on details,
check back as it will be periodically updated.

A short summary:

 * `-o ext_path=/mnt/somefile,ext_page_count=100`

Will start memcached with external storage in /mnt/somefile, with a page size
of 64 megabytes by default, and up to 100 pages of storage, which is 6.4
gigabytes. See `./memcached --help` for some other options, and the page above
for tuning detail.

 * This is a very flat, cache-oriented data shim. It is fast to forget but
   may not be as space efficient or write-amp efficient as a complex storage
   engine.
 * Data is not persistent. If restarted, all is lost (same as normal RAM
   cache).
 * The shim only helps with larger items; a data structure and the full key
   are always stored in RAM, and that object points to a position on disk for
   the value. The larger your items, the more you can benefit from this
   system.
 * Values are "aged" out of RAM; recent and popular items stay in RAM cache.
 * Pages are occasionally "compacted" when deletes or expired items leave too
   many holes in them. Configurable via `ext_max_frag`.
 * Does not presently work on ARM!

It can, or should, work in many scenarios. Since only older, colder items end
up on flash, you can use SSD-backed VM's and reduce the money you spend on RAM
caches. This will vary by use case.

See [Extstore](/Extstore) for full discussion on the tradeoffs. Please find us on the
mailing list if you have any questions.

### Contributors

The following people contributed to this release since 1.5.3.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.5.3..1.5.4` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.5.4

```
    55	dormando

```
