## Contents

- [Installation](#installation)
    - [From a package](#from-a-package)
    - [Problems with packages](#problems-with-packages)
    - [From source](#from-source)
    - [Install a client](#install-a-client)

<!-- end toc -->

## Installation
 
Memcached works on most Linux and BSD like systems. There is no official
support for windows builds.
 
### From a package

Generally, you should install memcached from a package provided by your
operating system. The OS will solve dependencies for you and take care of
security updates.

It's very simple. For Debian or Ubuntu:
```
apt-get install memcached
```

For Redhat/Fedora:
```
yum install memcached
```

See the documentation for your OS's package management system for more
information.

### Problems with packages

Most users run an older OS, such as an Ubuntu Long Term Support release from
several years ago or more. Please keep in mind that old OS's don't have newer
versions of memcached. You may be exposed to bugs or be missing on new
features. Even new versions of some OS's don't have the latest version
available.

If you run into bugs on using out of date software there isn't much we can do
for you. Please try to upgrade your package, or build from source.

### From source

Memcached depends on libevent. If you're running an OS installed after 2010
the package provided by your OS should be good enough.

```
apt-get install libevent-dev
```

```
yum install libevent-devel
```

...or whatever's necessary for your OS. If desired you can build from source as
well.

Installing from source:
```
wget https://memcached.org/latest
[you might need to rename the file]
tar -zxf memcached-1.x.x.tar.gz
cd memcached-1.x.x
./configure --prefix=/usr/local/memcached
make && make test && sudo make install
```

See `./configure --help` for full options. SASL support needs some optional
additional libraries.

### Install a client

Memcached on its own is simply a key/value store daemon. Installing it does
not automatically accelerate or cache any of your data: Your applications need
to be programmed to utilize the service. Applications will need clients, of
which there should be ones readily available for your language of choice.

