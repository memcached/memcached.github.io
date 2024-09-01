### Memcached 1.4.38 Release Notes

Date: 2017-6-24

### Download

http://www.memcached.org/files/memcached-1.4.38.tar.gz

### Overview

NOTE: This is probably the last release before `-o modern` becomes the default
and 1.5.0 is released.

The slab page automover algorithm should be much more fair. It now attempts to
keep the tail ages of slab classes similar. Please let us know how well this
works out!

The slab page mover can also delete items in order to make progress if it
detects a jam. Previously hot items could indefinitely hold the page mover.

The rest are minor fixes.

### Fixes

  * `hot_max_age` is now `hot_max_factor` - HOT is now limited to 20% of
    COLD's age or 20% of total space, whichever comes first.
  * sleep longer between slab move runs (1ms instead of 50us)
  * automove script: improve algo, add basic test
  * `slab_rebal`: delete busy items if stuck
  * fix LRU maintainer thread slowdown in edge case
  * fix rare long background thread pause in hash expansion


### New Features

The slab automover algorithm recently would only kick in if there was too many
free chunks in a slab class. If your items simply changed size over time this
was fine, but there were too many edge cases. If slab class 3 periodically
empties a lot of its memory, but slab class 5 will use as much as you have and
still evict, over time 3 will still become starved.

This new algorithm attempts to keep the youngest evicting slab class to within
80% of the age of the oldest slab class (by default). Ages are averaged over a
window to avoid ping-ponging memory between slab classe.

It also still aggressively frees memory if too much is free.

### Contributors

The following people contributed to this release since 1.4.37.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.37..1.4.38` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.38

```
     8	dormando

```
