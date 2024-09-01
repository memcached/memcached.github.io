### Memcached 1.6.14 Release Notes

Date: 2022-2-9

### Download

http://www.memcached.org/files/memcached-1.6.14.tar.gz

### Overview

Montly bugfix release. Important fix if you happen to have a couple billion
items in a single instance. A handful of useful fixes and a bunch of updates
to the upcoming proxy feature.

### Fixes

  * tests: workaround for t/watcher.t race
  * restart: fix typo in error message
  * Extend extbuf in `try_read_command_binary` function
  * tests: repair race in maxconns.t test
  * Fix integer overflow in hashsize calculation causing hang on huge hash tables
  * meta: add "proxy tokens" P, L which are ignored
  * core: fix large pages detection on redhat distros
  * Fix error message on conflicting ports while using '-l'

### New Features

None.

### Contributors

The following people contributed to this release since 1.6.13.

Note that this is based on who contributed changes, not how they were
done.  In many cases, a code snippet on the mailing list or a bug
report ended up as a commit with your name on it.

Note that this is just a summary of how many changes each person made
which doesn't necessarily reflect how significant each change was.
For details on what led up into a branch, either grab the git repo and
look at the output of `git log 1.6.13..1.6.14` or use a web view.

  * Repo list: https://github.com/memcached/memcached/wiki/DevelopmentRepos
  * Web View: http://github.com/memcached/memcached/commits/1.6.14

```
    24	dormando
     1	Baptiste Mille-Mathias
     1	David Carlier
     1	Gabe Van Engel
     1	Sailesh Mukil
     1	Tomas Korbar
     1	pkarumanchi9

```
