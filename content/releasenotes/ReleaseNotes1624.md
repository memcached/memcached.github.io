### Memcached 1.6.24 Release Notes

Date: 2024-2-27

### Download

http://www.memcached.org/files/memcached-1.6.24.tar.gz

### Overview

Mostly fixes from the fix backlog and proxy updates.

Note: if you use the experimental `-o slab_chunk_max` feature, the argument
has changed from bytes to kilobytes (as was documented in the help output).

If you use the debug `me` command, the expiration time was always negative.
That is now fixed.

### Fixes

  * core: fix issues with `-o slab_chunk_max=kb`
  * proto: fix exptime in debug command
  * fix: prevent giving negative number with -R, -m options
  * logger.c: initialize rport
  * crawler: include client flags in metadump output
  * extstore: fix CAS changing during recache
  * slabs: fix CAS changing during page move rescues
  * proxy: add global rate limiter
  * proxy: fix memory leaks when freeing funcgens
  * proxy: fix leak of `nil`s during reload
  * proxy: fix leak of unassigned fgens
  * proxy: fix router accounting in `stats proxyfuncs`
  * proxy: fix crash in `stats proxyfuncs`
  * proxy: fix bug in config reload cleanup phase
  * proxy: fix unlocked usage of config VM

### New Features

Nothing of note.

### Contributors

The following people contributed to this release since 1.6.23.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.23..1.6.24` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.24

```
    15	dormando
     1	Fabrice Fontaine
     1	Finn Frankis
     1	Ing-eoking

```
