+++
title = 'UDP DDoS'
date = 2024-09-04T14:58:40-07:00
+++

## Introduction

An amplification attack against memcached operates similarly to all DDoS amplification attacks such as [NTP or DNS amplification](https://en.wikipedia.org/wiki/Denial-of-service_attack#Amplification).
The attack works by sending spoofed requests to a vulnerable server, which then responds with a larger amount of data than the initial request, magnifying the volume of traffic.

This method of amplification attack is possible because memcached servers have the option to operate using the UDP protocol.
UDP is a network protocol that allows for the sending of data without first getting what’s known as a handshake, which is a network process where both sides agree to the communication.
UDP is utilized because the targeted host is never consulted on whether or not they’re willing to receive the data, allowing for a massive amount of data to be sent to the target without their prior consent.

## How does an attack with memcached work?

1. An attacker SETs a large item into an exposed memcached server.
1. Next the attacker spoofs a GET request with the IP address of the targeted victim.
1. The vulnerable memcached server that receives the request, sends a large response to the target.
1. The targeted server or its surrounding infrastructure is unable to process the large amount of data sent from the memcached server, resulting in overload and denial-of-service to legitimate requests.

## Mitigation

### Disable UDP

For memcached servers, make sure to disable UDP support if you do not need it.
UDP is disabled by default on versions 1.5.6 and later.

It can be disabled by adding `-U 0` to your start arguments. If you use a
config file, it may look like:

You can disable UDP by adding the following line to your `/etc/memcached.conf`:

```conf
# Disable UDP protocol
-U 0
```

Save `/etc/memcached.conf` and restart the service (On Ubuntu using `service memcached restart`).
You can check it worked by using `netstat`:

```text
$ sudo netstat -plunt

Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN      16079/memcached
```

As you can see, `memcached` is listening using only TCP.

### Firewall memcached servers

If your memcached server is only used by the local machine, you can limit the listening address to 127.0.0.1.
If other machines need to connect from a private network, force the listening on a private IP (for example 10.0.0.0.1, to be adapted to your network class).

You can change those settings in the file `/etc/memcached.conf`:

```conf
# Only listen on localhost
--listen 127.0.0.1
```

By default, Ubuntu and Debian bind Memcached to the local interface `127.0.0.1`.
You can also use firewalls to block external traffic from reaching your memcached server.
