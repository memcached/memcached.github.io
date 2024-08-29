### Memcached 1.4.37 Release Notes

Date: 2017-6-4

### Download

http://www.memcached.org/files/memcached-1.4.37.tar.gz

### Overview

A good number of small community fixes (see below). Number of crash fixes and
a 10 year old memory leak as well!

A new script (scripts/memcached-automove) is now included. This is a beta test
of the slab page automove algorithm we will be moving to, and will be enabled
by default when 1.5.0 lands soon. If you have time, please give it a test and
send us feedback!

### Fixes

  * LRU crawler: avoid running infinitely.
  * fix very old memory leak in ASCII multigets.
    (when using multiget `gets` and keys after the first one are >255 characters)
  * remove old slab mover example script.
  * fix crash in page mover while using large items
  * automover algo python script
  * avoid segfault if idle_timeout value is missing.
  * fix rare crash in LRU crawler
  * sleep more aggressively in some threads
  * don't overflow item refcount on get
  * fix solaris compilation error
  * usability fix for cache_memlimit command
  * fix verbose print for idle-kicker
  * disable refhang.t test due to flakiness
  * fix ordering issue in conn dispatch
    (prevents potential hangups)


### New Features

  * LRU crawler scheduling improvements

The LRU crawler's internal run scheduler still predated the segmented LRU. It
would run across each sub-LRU (HOT|WARM|etc), but schedule runs by slab class.

Now it schedules each sub-class independently. Now it will make more frequent
targeted runs. IE: If you make use of TEMP and store many items that expire
within a few minutes, it will wake up and re-run scans of each active TEMP LRU
instead of timing based on the total size of the slab class.

It can also re-schedule runs while a previous run is still running. In the
same example, it could continue to wake and scan TEMP LRU's, even if there's a
COLD LRU with 300 million items in it which could take minutes to scan by
itself.

  * per-LRU hits breakdown under `stats items` (`hits_to_(hot|warm|cold|temp)`)

### Contributors

The following people contributed to this release since 1.4.36.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.4.36..1.4.37` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.4.37

```
    17	dormando
     1	Fumihiro Ito
     1	Grant Mathews
     1	Josh Soref
     1	Peter (Stig) Edwards

```
