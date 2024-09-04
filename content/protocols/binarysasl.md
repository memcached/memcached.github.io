+++
title = 'Binary SASL Auth'
date = 2024-09-04T14:48:22-07:00
weight = 5
+++

## Overview
 
Most deployments of memcached today exist within trusted networks
where clients may freely connect to any server and the servers don't
discriminate against them.
 
There are cases, however, where memcached is deployed in untrusted
networks or where administrators would like to exercise a bit more
control over the clients that are connecting.

This does not provide encryption, but can provide authentication. Do not run
this over the internet large, do use this to protect from neighbors and
accidents from within large mostly trusted networks.
 
This page mostly exists to describe the protocol.

## Authentication Concepts
 
Authentication is abstracted from the server using the Simple
Authentication and Security Layer.
 
Among other things, this provides administrators with consistent
credential management that is mostly independent from the services
that are authenticating clients.
 
## Protocol Definitions
 
### Error Codes and Conditions
 
There are two status codes provided by the SASL protocol to enable
authentication:
 
#### Unauthorized
 
If a message is returned with a status code of `0x20`, this is
considered an authentication or authorization failure.
 
This may be in response to an explicit authentication command
indicating the credentials were not accepted or the authorization was
otherwise not granted to access the server.
 
#### Continue Authentication
 
Some SASL mechanisms require multiple messages to be sent between the
client and server. If a server responds to an authentication message
with a status code of `0x21`, this will indicate your client needs to do
more work to complete the authentication negotiation.
 
#### Authentication Not Supported
 
If a server responds to an authentication request indicating the
command is unknown (status `0x81`), it likely doesn't support
authentication. It is generally acceptable for the client to consider
authentication successful when communicating to a server that doesn't
support authentication.
 
### Authentication Requests
 
#### List Mechanisms
 
In order to negotiate authentication, a client may need to ask the
server what authentication mechanisms it supports.
 
A command `0x20` with no extras, key, or value will request a mechanism
list from the server. The mechanisms are returned as a
space-separated value.
 
#### Authentication Request
 
To begin an authentication request, send a request with command `0x21`,
the requested mechanism as the key, and the initial authentication
data as the value if any is required for the chosen mechanism.
 
#### Authentication Continuation
 
If the authentication request responded with a continuation request
(status `0x21`), the body will contain the data needed for computing the
next value in the authentication negotiation.
 
The next step's data will be transmitted similarly to the initial
step, but using command `0x22`. Note that this includes the mechanism
within the key as in the initial request.
 
## Error Reference

*Status Code* | *Meaning* |
| --- | --- |
0x20 | Authentication required / Not Successful |
0x21 | Further authentication steps required. |


## Command Reference

*Command* | *Op Code* | *Key* | *Value* |
| --- | --- | --- | --- |
 List Mechanisms | 0x20 | None | None |
Start Authentication | 0x21 | Mechanism | Auth Data |
Authentication Step | 0x22 | Mechanism | Auth Data |

## See Also
 
[memcached SASL howto](/SASLHowto)

http://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer

# HOWTO

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

Read more about memcached's [SASL auth protocol](/SASLAuthProtocol).
https://github.com/memcached/memcached/wiki/ReleaseNotes145#sasl_pwdb-for-more-simple-auth-deployments
