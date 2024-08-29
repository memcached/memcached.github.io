If you are developing, please CC the mailing list on any pull requests, or
simply post the code on the mailing list. There is a much wider audience
there.

## New contributors

Memcached can be a daunting project to get into. The codebase was started in
2003, and it is a stable long term project with many users. It is nearly
impossible to "clean up" or deprecate odd looking old bits, and new features
must have very high performance characteristics.

A big, probably easiest, contribution is to simply run newer versions.
For the last few years, an option `-o modern` has existed which flips feature
flags intended to become future defaults. While we try very hard to ensure the
stability of these new features, they are gated while we look for community
feedback.

This feedback often takes a long time to come in. If you run a cluster of
memcached servers and wish to contribute back, keep an eye on new releases and
run just a single instance on newer code. If you do this once a quarter, or
once a half, or simply ask the mailing list when a good time would be, you can
make a *huge* difference.

For code contributions; reading code, running tests, running mc-crusher,
reading commits, and submitting typo fixes are a great place to start. You
should already be familiar with C. You should also study libevent-style code
and multithreading basics. Keeping up with community and maintainer PR's is
also essential for understanding the direction of the codebase.

If you're looking for something to do, please ask the mailing list and give
some hint as to your ability or what kind of work you are looking for. There
are plenty of helpful tasks that have a lower barrier of entry.

## Branches

The `master` tree in the central repo should always contain runnable, high quality code. We go out of our way to ensure nothing goes to the central repo without a barrage of regression tests and code reviews.

The `next` tree is the integration branch for the next proposed release. As
PR's are tested/reviewed they are committed into this branch. If you are doing
feature work, please fork from and issue PR's against `next` if possible.

## Development work

Releases are tested using a slowly-increasing set of buildbots:
http://build.memcached.org:8010/ - due to costs, the buildbots are only
running during active development.

A little odd, but we expect that tests have been run *before*
PR's are opened. While this isn't always true for the full buildbot run, no
commits should be up for discussion if they do not pass the test suite. This
is different in comparison to projects which run CI against PR's.

Performance regression tests are run on hosts graciously donated by
https://www.packet.net/ - large many-core, NUMA, 144G+ RAM, 10g networking.
Each change is measured carefully to avoid performance and stability problems.
Some tests are run for many hours, even!

Performance tests are managed using mc-crusher:
https://github.com/dormando/mc-crusher - it's very simple to get the tool
running locally for testing, and you can still measure performance changes on
smaller hosts. For instance, I do first-pass tests using a dualcore broadwell NUC.

Any active contributor is welcome to share our development infrastructure.

## Why buildbots?

Portability. Our old buildbot network included several solaris hosts and a few
BSD types. As of this writing the BSD's are still missing but FreeBSD and
OpenBSD will be added back in. Memcached is shipped as part of a standard
package on many operating systems and it is important to validate portable
builds, even on ARM hosts!

## Central Github Repo

Web View: https://github.com/memcached/memcached

```
git clone git://github.com/memcached/memcached.git
```

## Active Development

We are actively developing against the 'next' branch.

## Developer repos:

 * dormando: git://github.com/dormando/memcached.git 

