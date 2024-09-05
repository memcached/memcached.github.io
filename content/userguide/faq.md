+++
title = 'FAQ'
date = 2024-09-04T14:41:27-07:00
weight = 2
+++

## Basics

### How can you list all keys?

You can list all keys using an interface that is deliberately limited.
Applications _must not_ rely on reading all keys back from a memcached server.
A server may have millions or billions of unrelated keys. An application that
relies on looking at all keys to then render a page will eventually fail.

You can use the `lru crawler` to examine all keys in an instance. This
interface provides useful data for doing an analysis on data that is stored in
a cache. See [the protocol
documentation](Protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt))
for full info.

### Why only RAM?

Everything memcached does is an attempt to guarantee latency and speed. That
said, it can make sense for some larger values to be fetched from high speed
flash drives. [A feature called extstore](/features/flashstorage/) allows
splitting items between RAM and disk storage.

### Why no complex operations?

All operations should run in O(1) time. They must be atomic. This doesn't necessarily mean complex operations can never happen, but it means we have to think very carefully about them first. Many complex operations can be emulated on top of more basic functionality.

### Why is memcached not recommended for sessions? Everyone does it!

If a session disappears, often the user is logged out. If a portion of a cache disappears, either due to a hardware crash or a simple software upgrade, it should not cause your users noticable pain. [This overly wordy post](http://dormando.livejournal.com/495593.html) explains alternatives. Memcached can often be used to reduce IO requirements to very very little, which means you may continue to use your existing relational database for the things it's good at.

Like keeping your users from being knocked off your site.

### What about the MySQL query cache?

The MySQL query cache can be a useful start for small sites. Unfortunately it uses many global locks on the mysql database, so enabling it can throttle you down. It also caches queries per table, and has to expire the entire cache related to a table when it changes, at all. If your site is fairly static this can work out fine, but when your tables start changing with any frequency this immediately falls over.

Memory is also limited, as it requires using a chunk of what's directly on your database.

### Is memcached atomic?

Aside from any bugs you may come across, all commands are internally atomic. Issuing multiple sets at the same time has no ill effect, aside from the last one in being the one that sticks.

### How do I troubleshoot client timeouts?

See [Timeouts](/troubleshooting/timeouts) for help.

## Setup Questions

### How do I authenticate?

Limited password based authentication is available in [the basic protocol](http://github.com/memcached/memcached/blob/master/doc/protocol.txt) - You can also enable TLS and authenticate by certificates verification.

### How do you handle failover?

You usually don't. Some clients have a "failover" option that will try the next server in the case of a failure.

- TODO: renovate this section.

### How do you handle replication?

It doesn't. Adding replication to the system halves your effective cache size. If you can't handle even a few percent extra cache misses, you have serious problems. Even with replication, things can break. More moving parts. Software to crash.

- TODO: renovate this section

### Can you persist cache between restarts?

Yes, in some situations. See [the documentation on warm restart](/features/restart/).

### Do clients and servers all need to talk to each other?

Nope. The less chatter, the more scalable the system.

## Monitoring

### Why Isn't curr_items Decreasing When Items Expire?

Expiration in memcached is lazy.  In general, an item cannot be known to be expired until something looks at it. This helps the server keep consistent performance.

Since 1.5.0 a background thread analyzes the cache over time and
asynchronously removes expired items from memory. [See this blog post for more detail](https://memcached.org/blog/modern-lru/)

## Use Cases

### When would you not want to use memcached?

It doesn't always make sense to add memcached to your application.

TODO: link to that whynot page here or just inline new stuff?

### Why can't I use it as a database?

Memcached is an ephemeral data store. Meaning if the server goes down (crash,
reboot, "cloud burps") then your data is gone. The ephemeral nature of the
software allows us to take extreme tradeoffs in design which allow us to be
10x, 100x, or even 1000x faster than a traditional database. Combining caching
with traditional datastores allows reducing cost and improving user
experience.

### Can using memcached make my application slower?

Yes, absolutely. If your DB queries are all fast, your website is fast, adding memcached might not make it faster.

Also, this:

```
my @post_ids = fetch_all_posts($thread_id);
my @post_entries = ();
for my $post_id (@post_ids) {
	push(@post_entries, $memc->get($post_id));
}
# Yay I have all my post entries!
```

Instead of this anti-pattern, use pipelined gets instead. Fetching a single item from memcached still requires a network roundtrip and a little processing. The more you can fetch at once the better.
