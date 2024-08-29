### Memcached 1.6.25 Release Notes

Date: 2024-3-19

### Download

http://www.memcached.org/files/memcached-1.6.25.tar.gz

### Overview

Main focus of this release is an overhaul of extstore's space compaction
system, and addition of disk specialization and basic tiering support. This
optimization can greatly reduce write amplification and improve general
performance. See below for details.

The `stats sizes_enable` and `stats sizes_disable` commands have been removed
as they could cause the daemon to crash. The feature can be enabled at start
time via `-o track_sizes`. You can also generate a histogram of data sizes via
the `lru_clawler metadump` command and a script, which does not block the server.

The `flush_all` command was adjusted to be more accurate with CAS disabled.

This also addresses performance issues related to proxy lua scripting.

### Fixes

  * core: remove `stats sizes_enable|disable`
  * core: stop using cas for `flush_all`
  * proxy: run lua GC outside of request path
  * proxy: fix leak in config reload
  * proxy: allow early freeing rcontexts
  * proxy: add new request meta flag APIs
  * fix build on uclibc-ng

### New Features

This release adds the concept of assigning data storage to specialized
"buckets" in extstore. This can be used to create tiered storage between
new and old/cold data. You can also tier between normal, compacted, "low
ttl" and so on.

run with: `ext_path=/path/f1:64m,ext_path=/path2/f2:128m:compact`
creates a 64m file for "Default" pages and a 128m file for compacted
pages. Also works with 'chunked' and 'lowttl'.

tiered storage:

- If the above bucket is 'old' (ie; `ext_path=/etc:1G:old`) then when the
  "main" disk is full, valid items are moved to the "old" bucket to free
  up space in the main disk. This allows using cheaper storage for old
  data. Especially useful if cache is an A/B data load.

- If the bucket is 'coldcompact' then the behavior is similar to 'old',
  except that it will only move items that are in the COLD LRU.

- If old or coldcompact are full, the oldest page is evicted.

Both of these features can be combined at once. Old active data could go
to more expensive drives and cold data could go to cheaper networked
drives.

Improvements:
- fixes off-by-one preventing first page in each file from being used.
- makes page eviction inline with allocation, making it more responsive
  when necessary.
- removes the "fragmentation slew" calculation from when compaction
  should kick in. Instead of writing early, should wait until we've hit
  the `compact_under` page limit then start compacting the emptiest pages.
- begins compaction when free pages are nearly all gone, instead of when
  fewer than 25% of pages are free.
- defragments the _most fragmented page_ first instead of the oldest
- uses a dedicated IO thread for data writes and compaction IO,
  improving tail latency.
- Runs the compaction checker algorithm less often, saving CPU.

### Contributors

The following people contributed to this release since 1.6.24.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.24..1.6.25` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.25

```
    11	dormando
     1	Fabrice Fontaine
     1	Qu Chen

```
