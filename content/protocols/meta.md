+++
title = 'Meta Text Protocol'
date = 2024-09-04T14:48:18-07:00
weight = 2
+++

NOTE: These commands are new. Please let us know if you run into any trouble
with the API or the documentation! The meta protocol is no longer considered
experimental, please give it a shot!

NOTE: binary protocol is deprecated. meta protocol is cross-compatible with
the text protocol, includes every feature the binary protocol has, and has
many enhancements.

Memcached has additional commands which are used to reduce bytes on the wire,
reduce network roundtrips required for complex queries (such as anti-dogpiling
techniques), expose previously hidden item information, and add many new
features. These are in addition to the existing Text Protocol, and can do
everything the Binary Protocol could do before.

The full description of the Meta commands are available in the Text Protocol
documentation:
 * [Text Protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt)

This wiki serves as a companion to protocol.txt with use cases and examples of
the new commands.

## Command Basics

Commands and responses start with a two character code then a set of flags, and
potentially value data. Flags may have token data attached to them.
```
set request:
 ms foo 2 T90 F1\r\n
 hi\r\n
response:
 HD\r\n

get request:
 mg foo t f v\r\n

response:
 VA 2 t78 f1\r\n
 hi\r\n

delete request:
 md foo\r\n

response:
 HD\r\n
```

Commands are 2 characters, followed by key, flags, and tokens requested by
flags.
Responses are a 2 character code, any
additional flags added (for mg), and any requested tokens. Flag responses
are in the order set by the order in the client request.

For full detail please see [Text
Protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt)
documentation.

## Replacing GET/GETS/TOUCH/GAT/GATS

Standard GET:

```
mg foo f v\r\n -- ask for client flags, value
VA 2 f30\r\n -- get length, client flags, value
hi\r\n
```

... will return client flags, value. Add `k` to also get the key back.

GETS (get with CAS):

```
mg foo f c v\r\n
VA 2 f30 c3\r\n -- also gets the CAS value back
hi\r\n
```

TOUCH (just update TTL, no response data):

```
mg foo T30\r\n -- update the TTL to be 30 seconds from now.
HD\r\n -- no flags or value requested, get HD return code
```

GAT (get and touch):

`mg foo f v T90`

... will fetch client flags, value and update the TTL to be 90 seconds from now.

GATS (get and touch with CAS):

`mg foo f c v T100`

... same as above, but also gets the CAS value.

## New MetaData Flags

- `l` flag will show the number of seconds since the item was last
accessed.
- `h` flag will return 0 or 1 based on if the item has ever been fetched since
being stored.
- `t` flag will return the number of seconds remaining until the item expires
(-1 for infinite)

Others will be documented in protocol.txt

## Binary Encoded Keys

With meta protocol it is possible store and retrieve non-ascii keys. This can
save RAM on servers if you have keys that are very long, but data that are
very short. You can also store UTF8 strings this way.

For example, your key may look like: "string-12345678910-9999313149" or
"reallylongidentifierstringbecausewehavealotofthese-[ID number]. To save
memory the string identifier can be encoded into a small number, and ID's can
be 32bit or 64bit integers.

In order to store/retrieve encoded keys, they must be BASE64 encoded. This
adds some overhead on the network portion but typically extreme fast to encode
or decode on modern hardware.

```

binary key set request: ("tesuto" in japanese characters)
 ms 44OG44K544OI 2 b\r\n
 hi\r\n

 mg 44OG44K544OI b v k\r\n
 VA 2 k44OG44K544OI b\r\n
 hi\r\n

```

## Atomic Stampeding Herd Handling

We can use the CAS value and some new flags to do stampeding herd (dogpiling)
protection with a minimal number of round trips to the server:

```
request:
 mg foo f c v N30\r\n
response:
 VA 0 f0 c2 W\r\n
\r\n
```

The `N` flag instructs memcached to automatically create an item on miss, with
a supplied TTL, which is 30 seconds in this example.

In the flags returned a new flag `W` has been added. This is instructing the
client that it has received a miss and has "Won" the right to recache the
item. The client may then directly update the value once it has been fetched
or calculated, or set back using the CAS value it retrieved.

If a different client requests the same item in this time, it will see a slightly
different response:

```
request:
 mg foo f c v N30\r\n
response:
 VA 0 f0 c2 Z\r\n
\r\n
```

The `Z` flag indicates to the client that a `W` flag has already been sent,
and this is a special non-data item. The client may then retry, wait, or try
something else.

This approach was done in the past by having clients race with
an `add` command after a miss, using special response values, or so on. It is
now collapsed into the normal request/response workflow, and has additional
features.

## Early Recache

The 'R' flag can be used to do an anti-herd early recache of objects nearing
their expiration time. This can be used to reduce misses for frequently
accessed objects. Infrequently accessed objects can be left to expire.

```
request:
 mg foo v t R30\r\n
response:
 VA 2 t29 W\r\n
hi\r\n
```

In this example, the R flag is supplied a token of '30', meaning if the TTL
remaining on an item is less than 30 seconds, attempt to refresh the item.

In the response we see the extra 'W' (win) flag has been returned, indicating
to this client that it should recache the item. Any further clients will
instead see a 'Z' flag, indicating a request has already been sent.

We also see the TTL remaining, via the 't' flag, confirming that the TTL is
below 30 seconds.

The CAS value may also be requested via the 'c' flag, which allows honoring
the recache only if the object hasn't changed since the win token was
received.

```
get request:
 mg foo v c R30\r\n
response:
 VA 0 c999\r\n
\r\n

set request:
 ms foo 3 C999
 new\r\n
response:
 HD\r\n
```

## Serve Stale

Intentionally serving stale but usable data is possible with the meta commands, similar to Stale-While-Revalidate
or Stale-While-Error in HTTP.

In some cases you would want to actively mark an
in-memory item as stale. You can then either have the first client fetching
the stale value also handle revalidation, or kick off an asynchronous recache
but still inform clients that the data may not be up to date.

We use the meta delete command to mark an item as stale.

```
request:
 md foo I T30\r\n
response:
 HD\r\n
```

We also optionally change the TTL. In this instance stale data will be served
for a maximum of 30 seconds. Marking an item as stale also changes its CAS ID
number.

```
request:
 mg foo t c v
response:
 VA 4 t29 c777 W X\r\n
 data\r\n
```

The next metaget gets the 'W' flag, indicating it has rights to exclusively
recache the item. It also gets the 'X' flag, indicating that the item is
stale. How this is handled is up to the application; it can use the data
as-is, adjust it, warn a user, or quietly re-fetch later.

If another metadelete comes in before the item above is recached, the CAS will
change to 778 (or higher), and the later metaset call will fail.

However, it is possible to still update the item but keep it marked as stale,
via the 'I' flag with metaset.

```
request:
 ms foo S3 T360 C777 I\r\n
 new\r\n
response:
 ST\r\n
```

The next metaget will continue to see the item as stale, with its previous
TTL and previous CAS:

```
request:
 mg foo t c v\r\n
response:
 VA 3 t25 c778 X\r\n
 new\r\n
```

The CAS value must be _lower_ than the real CAS value for this to apply. Once
a set matching the CAS value comes in, all data is overwritten and the TTL is
updated properly.

## Pipelining Quiet mode with Opaque or Key

Pipelining with meta commands is done with combinations of the 'q', 'O', and
'k' flags. These flags work for all of the metaget, metaset, and metadelete
commands.

In the normal text protocol, the requested key is reflected back to the
client. This makes it possible to differentiate responses when requesting many
keys.

```
get bar foooooooooooooooooooooooooooooooo baz
VALUE foooooooooooooooooooooooooooooooo 0 2
hi
END
```

In the above, only foo+ exists. The rest of the values don't have lines
indicating a miss, only the END token after all keys are fetched.

This is still true even when fetching a single key:

```
get foooooooooooooooooooooooooooooooo
VALUE foooooooooooooooooooooooooooooooo 0 2
hi
END
```

Metagets do not have a multi-key interface. Requesting many keys at once
requires pipelining the commands:

```
mg foo v\r\nmg bar v\r\nmg baz v\r\n
```

Metaget will also not return the key by default. Clients should look for the
response codes to count responses.

It's possible to optimize fetching many keys by using the 'q' flag, which will
hide the "EN" code on miss. There is a problem with this: you can no longer
differentiate the responses to match item data to requested keys.

There are two options for optimizing pipelined requests:

```
request:
 mg foo t c v q k\r\n
response:
 VA 2 s2 t-1 c2 kfoo\r\n
 hi\r\n
```

The 'k' flag will add the key as a token in the response. This works for `mg`,
as well as `ms` and `md`.

This is still not ideal if your keys are very long, especially if the data
stored is very small (perhaps even 0 bytes!). Returning 200 byte keys with 8
bytes of data is a lot of extra bytes on the wire.

There is one more option, the 'O' (opaque) flag. Tokens supplied with this
flag are reflected back in the response as-is. Opaque tokens can be up to 32
bytes in length as of this writing. They can be alphanumeric but numbers are
likely the common use case.

```
request:
 mg foo v q Oopaque\r\n
response:
 VA 2 Oopaque\r\n
 hi\r\n
```

In this example the string "opaque" is used as a token to make it stand out
more in the response. This can be any (short) ascii string. A simple approach
would be to simply count the number of outstanding requests, using numerics
and resetting after receiving the responses. This keeps the numbers short,
with some benefit from hex encoding if desired.

Finally, it's still impossible to tell when all of the keys have been
processed if they were all misses. There are two options:

1) Send the final mg without the 'q' flag, which will add an EN response code.
2) Use the `mn` meta no-op command. All this command does is respond with a
bare MN code.

```
request:
 mn\r\n
response:
 MN\r\n
```

Stick it at the end of a pipeline:

`mg [etc]\r\nmg [etc]\r\nmg [etc]\r\nmn\r\n`

## Quiet mode semantics

The 'q' flag is described briefly in the section about Pipelining, but what
else does it do?

In general, the 'q' flag will hide "nominal" responses. If you wish to
pipeline a bunch of sets together but don't want all of the "ST" code
responses, pass the 'q' flag with it. If a set results in a code other than
"ST" (ie; "EX" for a failed CAS), the response will still be returned.

Any syntax errors will still result in a response as well (`CLIENT_ERROR`).

## Data consistency with CAS overrides

### Data version or time based consistency

After version 1.6.27 the meta protocol supports directly providing a CAS value
during mutation operations. By default the CAS value (or CAS id) for an item
is generated directly by memcached using a globally incrementing counter, but
we can now override this with the `E` flag.

For example, if the data you are caching has a "version id" or "row version"
we can provide that:

```
ms foo 2 E73 -- directly provide the CAS value
hi
HD
mg foo c v -- later able to retrieve it
VA 2 c73
hi
```

We have directly set this value's CAS id to `73`. Now we can use standard CAS
operations to update the data. For example, attempting to update the data with
an older version will now fail:

```
ms foo 2 C72 E73
hi
EX
```

The above could be the result of a race condition: two proceses are trying to
move the data from version 72 to 73 at the same time. Since the underlying
version is already 73, the second command will fail.

Note that any command which can generate a new cas id also accepts the `E`
flag. IE: delete with invalidate, ma for incr/decr, and so on.

#### Time and types

Anything that fits in an 8 byte _incrementing_ number can be used as a CAS id:
versions, clocks, HLC's, and so on. So long as the next number is higher than
the previous number.

### Data consistency across multiple pools

If you are attempting to keep multiple pools of memcached servers in sync, we
can use the CAS override to help improve our consistency results. Please note
this system is not strict.

#### Leader and follower pools

Assume you have pools A, B, C, and one pool is designated as a "leader", we
can provide general consistency which can be also be repaired:

```
-- against pool A:
mg foo c v\r\n
VA 2 C50\r\n
hi\r\n
-- we fetched a value. we have a new row version (or timestamp):
ms foo 2 C50 E51\r\n
ih\r\n
HD\r\n
-- Success. Now, against pools B and C we issue "blind sets":
-- pool B:
ms foo 2 E51\r\n
ih\r\n
HD\r\n
-- pool C:
ms foo 2 E51\r\n
ih\r\n
HD\r\n
```

If all is well all copies will have the same CAS ID as data in pool A. This is
again, not strict, but can help verify if data is consistent or not. If the
data in pool A has gone missing, you can decide on quorum or highest ID to
repair data from B/C. Or simply not allow data to change until A has been
repaired, but in the meantime data can be read from B/C.

#### Full cross consistency

It is difficult if not impossible to guarantee consistency across multiple
pools. You can attempt this by:

```
-- against pool A:
mg foo c v\r\n -- if we need the value. else drop the v.
-- against pools B/C:
mg foo c\r\n -- don't necessarily need the value
-- use the same set command across all three hosts:
ms foo 2 Cnnnn E90\r\n
hi\r\n
```

If this fails on any host, do the whole routine again. If a value is being
frequently updated this can be problematic or permanently blocking.

You can augment this with the stale/win flags by picking a "leader" pool and
issuing an invalidation:

```
md foo I E74\r\n -- invalidate the key if it exists, prep it for the new ver
HD\r\n
mg foo c v\r\n
VA 2 c75 X W\r\n -- data is stale (X) and we atomically win (W)
hi\r\n
```

Now this client has the exclusive right to update this value across all pools.
If updates to pools B/C end up coming _out of order_ the highest CAS value
should eventually succeed if the updates are asynchronous:

- If pool B starts at version 70, then gets updated to 75 as above
- A later update to 73 will fail (CAS too old)
- If pool C starts at version 70, gets updated to 73, then gets updated to 75
- The final result will be the correct version

If the client waits for either quorum (2/3 in this case) or all (3/3) hosts to
respond before responding to its user, the data should be the same.

Problems where this fails should be relatively obscure, for example:
- Pool A is our leader, we get win flag (W) and successfully update it.
- We update Pools B, C, but in the meantime pool A has failed.
- Depending on how new leaders are elected and how long updates take, we can
  end up with inconsistent data.

In reality host or pool election is slow and cross-stream updates are
relatively fast, so it should be unusual.

## Probabilistic Hot Cache

A new technique made possible with the meta commands is a probabilistic
client-side hot key cache. This means a coordination-free method of populating
a local cache to avoid making requests to memcached for very frequently
accessed items.

Using the new `h` and `l` flags, we can see if an item has been hit before,
and how many seconds it's been since it was last hit. Lets combine these:

```
request:
 mg foo v h l\r\n
response:
 VA 4 h1 l5\r\n
 data\r\n
```

Breaking down the flags:
- `s` is data size, getting a 4 in the response.
- `v` means return the value, getting "data\r\n" in the response block.
- `h` means return a 0 or 1 depending on if the item has been requested since
  it was stored.
- `l` means the time in seconds since last access, which here shows 5 seconds.

We can weight these values to create a probabilistic cache:
`if (h == 1 && l < 5 && random(1000) == 0) { add_to_local_cache(it); }`

The random factor here is a weight that could be other factors (database/network/system load/local cache hit rate/etc). Keys which suddenly get a lot of traffic will filter in slowly, with the highest traffic keys being cached very quickly.

This approach requires no live coordination between servers to discover or
communicate "hot keys". Implementations of this method do need to make a
decision on how validation is done.

### Hot Key Cache Invalidation

For truly hot keys, the simplest approach is to only cache them in a client
for a couple seconds. A "shadow key" with a slightly longer expiration time
(30 seconds or so) could be left in its place to signal to a
client to immediately recache a key if seen again, This covers a wide number
of use cases. The total number of requests to memcached will be higher than in
a perfect system, but the zero coordination effort and natural key discovery
without server overhead is a huge bonus.

Another approach, especially useful for items which are large or are CPU
intensive to deserialize, is to periodically revalidate items.

```
request:
 mg foo v h l c\r\n
response:
 VA 4 h1 l5 c500\r\n
 data\r\n
```

This time we also request the CAS value. A client could schedule asynchronous
periodic revalidations for hot keys in the background. Metaget can fetch
items without the value.

```
request:
 mg foo c\r\n
response:
 HD c500
```

(the `HD` status code indicates no value is being received; only a header)

In this case, we only care about finding if the CAS value is identical.

We may add support for conditionally fetching the value if CAS does or does
not match, to further improve this use case. Check doc/protocol.txt with your
tarball for the most up to date information.
