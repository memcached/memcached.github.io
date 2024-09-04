+++
title = 'Server Guide'
date = 2024-09-02T11:17:41-07:00
draft = true
weight = 1
next = '/serverguide/configuring/'
+++

## Installation

Memcached works on most Linux and BSD like systems. There is no official
support for windows builds.

### From a package

You should install memcached from a package provided by your
operating system. The OS will solve dependencies for you and take care of
security updates.

For Debian or Ubuntu:
```
apt-get install memcached
```

For Redhat/Fedora:
```
yum install memcached
```

For Mac:
```
brew install memcached
```

See the documentation for your OS's package management system for more
information.

### Potential issues with packages

The version of memcached provided by your OS's package manager may be out of
date. If you are running an OS that can be several years old (such as ubuntu LTS), the provided version of memcached may be significantly out of date.

You may be missing out on features or bug fixes if the OS version is very old.
Your package manager may have options for providing a newer version, you can
use Docker, or memcached can be built from source.

### From source

Memcached depends on libevent and a C compiler.

```
apt-get install build-essential libevent-dev
```

```
yum install libevent-devel
```

...or whatever's necessary for your OS.

Installing from source:
```
wget https://memcached.org/latest
[you might need to rename the file]
tar -zxf memcached-x.x.x.tar.gz
cd memcached-x.x.x
./configure --prefix=/usr/local/memcached
make && make test && sudo make install
```

See `./configure --help` for full options.

### Install a client

Memcached on its own is just a key/value storage daemon. Installing it does
not automatically accelerate or cache any of your data: Your applications need
to be programmed to utilize this service. Applications will need clients, of
which there should be ones readily available for your language of choice.

