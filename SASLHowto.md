## Introduction

If running memcached in a mostly trusted network; such as within a cloud or a
corporate internal cloud, you might want to restrict access to the service.

SASL is not implemented as end-to-end encryption. While this can facilitate
restricting access to the daemon, it does not hide communications and isn't
suitable for using over the internet.

[SASL](http://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) (as described in [RFC2222](http://tools.ietf.org/html/rfc2222)) is a standard for adding authentication mechanisms to protocols in a way that is protocol independent.

## Getting Started

In order to deploy memcached with SASL, you'll need two things:

* A memcached server with SASL support (version 1.4.3 or greater built with `--enable-sasl`)
* A client that supports SASL
  * The server side has to support the authentication mechanism the client is using, 
    some clients only support `plain` while an OS may not provide a library/package that
    supports `plain`, at least not by default.

### Configuring SASL

For the most part, you just do the normal SASL admin stuff.

```
# Create a user for memcached.
saslpasswd2 -a memcached -c cacheuser
```

### Running Memcached

In order to enable SASL support in the server you must use the `-S` flag.

The `-S` flag does a few things things:

* Enable all of the SASL commands.
* Require binary protocol _only_.
* Require authentication to have been successful before commands may be issued on a connection.

### SASL how to FAQ

#### Q.) How can I check if the memcached server I have is built with SASL support?
  * A.) You can see if the command line help lists the option:
  
  ```
        -S, --enable-sasl         turn on Sasl authentication
  ```
    
  * A.) You can run the server in verbose mode, for example:
  
  ```
        > memcached -S -vvvv 
        Reading configuration from: <...memcached.conf>
        Initialized SASL.
  ```
  
  * A.) On Linux you can check if it links against a SASL library, for example:
  
  ```
        > ldd `which memcached` | grep -i sasl
        libsasl2.so.3 => /lib64/libsasl2.so.3 
  ```
  
#### Q.) The configure option `--enable-sasl-pwdb` is not working
  * A.) See https://github.com/memcached/memcached/issues/365

#### Q.) Plain auth is not working, why not?
  * A.) Does running  memcached with the `-vvvv` option provide any useful messages?
  * A.) Check you have the SASL lib that implements it installed, for example `/usr/lib64/sasl2/libplain.so`
        provided by the RPM `cyrus-sasl-plain`. 
  * A.) Check the password file is owned by and only readable by the user running memcached.
  * A.) Check that the username the client is using matches that used in the password file,
        note that sometimes the client hostname is postfixed to the username with an @, so
        `myname@client_hostname:mypass`

### Further Info

Read more about memcached's [[SASL auth protocol|SASLAuthProtocol]].
https://github.com/memcached/memcached/wiki/ReleaseNotes145#sasl_pwdb-for-more-simple-auth-deployments
