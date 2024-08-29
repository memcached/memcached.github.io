Memcached supports a basic Text and Meta Text protocol. There is also a
deprecated "binary" which no longer receives updates, but was used for a long
time. The Meta protocol is more efficient than the binary protocol, is cross
compatible with the Text protocol, and is recommended for all new clients.

 * [Text and Meta Protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt)
 * [[Meta Examples|MetaCommands]]
 * [[Binary Protocol|BinaryProtocolRevamped]]
   * [Slides on binary protocol ](http://www.slideshare.net/tmaesaka/memcached-binary-protocol-in-a-nutshell-presentation/) by Toru Maesaka (2008)

Further, there are sub protocols and proposals

 * [[SASL Authentication|SASLAuthProtocol]]

## Why is the binary protocol deprecated?

The binary protocol was introduced in 2008. It seemed like a good idea but it was not:

- Security issues
- Poor efficiency prevented adoption by large users
- Limited features and poor extensibility
- SASL authentication rarely worked and caused a lot of support headaches.
- Difficult to implement

We introduced the meta protocol in 2019:

- Better wire efficiency
- More features (anti-stampeding herd, stale-while-revalidate, etc)
- Small command set (get/set/delete/math)
- Extensible (flags and tokens)
- Simple and easy to implement

More information can be found in `protocol.txt` linked above.
