### Memcached 1.4.36 Release Notes

Date: 2017-3-19

### Download

http://www.memcached.org/files/memcached-1.4.36.tar.gz

### Overview

Important bug fix that could lead to a hung slab mover. Also improves memory
efficiency of chunked items.

### Fixes

  * fix refcount leak in LRU bump buf


### New Features

Chained items were introduced in 1.4.29. With `-o modern` items sized 512k or
higher are created by chaining 512k chunks together. This made increasing the
max item size (`-I`) more efficient in many scenarios as the slab classes no
longer have to be stretched to cover the full space.

There was still an efficiency hole for items 512k->5mb or so where the overhead
is too big for the size of the items. This change fixes it by using chunks
from other slab classes in order to "cap" off chained items. With this change
larger items should be more efficient than the original slab allocator in all
cases.

Chunked items are only used with `-o modern` or explicitly changing `-o slab_chunk_max`
It is not recommended to set `slab_chunk_max` to be smaller than 256k at this
time.

### Contributors

The following people contributed to this release since 1.4.35.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.35..1.4.36` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.36

```
     2	dormando

```
