## TLS

- [Quick Start](#quick-start)
- [Performance](#performance)
- [Unsupported features](#unsupported-features)
- [Future plans](#future-plans)
- [Rough notes](#rough-notes)

---

This feature is still in experimental stages. Comprehensive documentation will be filled out over time. See `doc/tls.txt` for design and some implementation details.

## Quick Start

Memcached  1.5.13 supports authentication and encryption via TLS. This requires: 
- OpenSSL 1.1.0 or later
- Memcached client which can communicate over TLS
- Server built using ./configure --enable-tls.


### When to use this?

- Encryption: data is required to be encrypted on the wire between clients and the server.
- Authentication : server and client are required to authenticate each other. 
- Data integrity: ensure data is not tampered or altered when transmitted between client and server.

### How to use it?

Grab the latest memcached.

Build with `./configure --enable-tls && make && make test && sudo make install`

Obtain a valid certificate and a key

Use your normal startup options and add  `-Z -o ssl_chain_cert=/home/user/cert,ssl_key=/home/user/key`

`-Z` (or `--enable-ssl`) enables the TLS and `ssl_chain_cert` and `ssl_key` to specify the cert and the key.

If you have trouble starting the server, use verbose levels ('memcached -v') to print errors and warnings.

If there are no errors, all TCP interfaces you’ve specified support TLS. 

You can quickly test the server with OpenSSL `s_client`.

`$ openssl s_client -connect host:port`

### Other options
You can obtain all available options using the `memcached -h`

If required, you can specify a different private key format using `-o ssl_keyformat=`. The default format is PEM (1). This value has to be an integer. Refer to OpenSSL certificate formats for more details.

To change the peer certificate verification mode, use `-o ssl_verify_mode=`. Default is 0 (None). Valid values are 0 (None), 1 (Request), 2 (Require) or 3 (Once). Refer OpenSSL verification modes for more details.

To limit supported ciphers, use `-o ssl_ciphers=`. Refer OpenSSL cipher names for valid options. 

If required, provide a CA cert using `-o ssl_ca_cert=`. Only PEM file format is accepted.

If you invalidate old server certificates, received new ones and requires memcached to use them, you don’t need to restart the server.
Connect with the server and run the `refresh_certs` command. 
Memcached will reload new certificates (cert name and paths has to be same as the original ones) and new connections will use new certificates.
Existing connections will continue to use session keys created using old certificates.

You can opt out TLS for certain interfaces (e.g. use plain text on localhost). This can be specified via `-l` or `--listen=`. Add `notls:` prefix to the interfaces that don’t require TLS.
E.g. `-l notls:<host>:<port>`

### Monitoring
You can increase the verbosity of the server to print detailed error/warning messages.

`stats settings` command will show the current TLS settings, prefixed with `ssl_`.

`stats` will print `time_since_server_cert_refresh`. This shows the number of seconds elapsed since server loaded certificates. This is helpful when you need to refresh certificates.


### How to tune it?

The current TLS implementation splits large messages (more than 16K by default) and send them in chunks. 

This limit can be increased via `--o ssl_wbuf_size=` option. The size is in KB and the default is 16KB.
This buffer is allocated at each worker thread level; hence you can calculate the memory budget (in KB) by `ssl_wbuf_size * number of worker threads`.


---

## Performance

`TBD`


---
## Unsupported features
- Session resumption
- Renegotiation


---

## Future plans

- Evaluate Amazon s2n (s2n is a C99 implementation of the TLS/SSL protocols)


---
## Rough notes

See `doc/tls.txt` for design and some implementation details.
See `memcached -h` for start arguments.
Run `./configure --enable-tls` to compile the feature in.

