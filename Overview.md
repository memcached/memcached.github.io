## Contents

- [Memcached](#memcached)
    - [How Does It Work?](#how-does-it-work)
    - [What is it Made Up Of?](#what-is-it-made-up-of)
- [Design Philosophy](#design-philosophy)
    - [Simple Key/Value Store](#simple-keyvalue-store)
    - [Logic Half in Client, Half in Server](#logic-half-in-client-half-in-server)
    - [Servers are Disconnected From Each Other](#servers-are-disconnected-from-each-other)
    - [O(1)](#o1)
    - [Forgetting is a Feature](#forgetting-is-a-feature)
    - [Cache Invalidation](#cache-invalidation)

<!-- end toc -->

## Memcached

*Free & open source, high-performance, distributed memory object caching system*, generic in nature, but intended for use in speeding up dynamic web applications by alleviating database load.

Memcached is an in-memory key-value store for small arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.

*Memcached is simple yet powerful*. Its simple design promotes quick deployment, ease of development, and solves many problems facing large data caches. Its API is available for most popular languages.

See the [memcached.org about page](http://memcached.org/about) for a brief overview.

### How Does It Work?

Memcached is a developer tool, not a "code accelerator", nor is it database middleware. If you're trying to set up an application you have downloaded or purchased to use memcached, read your app's documentation. This wiki and community will not be able to help you.

### What is it Made Up Of?

 * Client software, which is given a list of available memcached servers.
 * A client-based hashing algorithm, which chooses a server based on the "key".
 * Server software, which stores values with their keys into an internal hash table.
 * LRU, which determine when to throw out old data (if out of memory), or reuse memory.

---

## Design Philosophy

### Simple Key/Value Store

The server does not care what your data looks like. Items are made up of a key, an expiration time, optional flags, and raw data. It does not understand data structures; you must upload data that is pre-serialized. Some commands (incr/decr) may operate on the underlying data, but in a simple manner.

### Logic Half in Client, Half in Server

A "memcached implementation" is partially in a client, and partially in a server. Clients understand how to choose which server to read or write to for an item, what to do when it cannot contact a server.

The servers understand how to store and fetch items. They also manage when to evict or reuse memory.

### Servers are Disconnected From Each Other

Memcached servers are unaware of each other. There is no crosstalk, no syncronization, no broadcasting, no replication. Adding servers increases the available memory. Cache invalidation is simplified, as clients delete or overwrite data on the server which owns it directly.

### O(1)

All commands are implemented to be as fast and lock-friendly as possible. This gives allows near-deterministic query speeds for all use cases.

Queries on slow machines should run in well under 1ms. High end servers can serve millions of keys per second in throughput.

### Forgetting is a Feature

Memcached is, by default, a Least Recently Used cache. Items expire after a specified amount of time. Both of these are elegant solutions to many problems; Expire items after a minute to limit stale data being returned, or flush unused data in an effort to retain frequently requested information.

No "pauses" waiting for a garbage collector ensures low latency, and free space is lazily reclaimed.

See [LRU documentation](https://github.com/memcached/memcached/blob/master/doc/new_lru.txt) for more details on the latest algorithm.

### Cache Invalidation

Rather than broadcasting changes to all available hosts, clients directly address the server holding the data to be invalidated.
