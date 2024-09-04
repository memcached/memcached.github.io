+++
title = 'Protocols'
date = 2024-09-04T14:38:41-07:00
weight = 4
+++

Memcached supports a basic Text and Meta Text protocol. There is also a
deprecated "binary" which no longer receives updates.
The Meta protocol is more efficient than the older binary protocol, is cross
compatible with the Text protocol. It is recommended for all new clients.

 * [Text and Meta Protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt)
 * [Meta Examples](/protocols/meta/)
 * [Binary Protocol](/protocols/binary/)
   * [Slides on binary protocol ](http://www.slideshare.net/tmaesaka/memcached-binary-protocol-in-a-nutshell-presentation/) by Toru Maesaka (2008)

Further, there are sub protocols and proposals

 * [Binary protocol SASL Authentication](/protocols/binarysasl/)

## Why is the binary protocol deprecated?

The binary protocol was introduced in 2008. It seemed like a good idea but it
had many issues:

- Security issues
- Poor efficiency prevented adoption by large users
- Limited features and poor extensibility
- SASL authentication rarely worked and caused a lot of support headaches.
- Difficult to implement as a client.

We introduced the meta protocol in 2019:

- Better wire efficiency
- More features (anti-stampeding herd, stale-while-revalidate, etc)
- Small command set (get/set/delete/math)
- Extensible (flags and tokens)
- Simple and easy to implement

More information can be found in `protocol.txt` linked above.
