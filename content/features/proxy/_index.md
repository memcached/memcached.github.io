+++
title = 'Built-in proxy'
date = 2024-09-01T09:31:55-07:00
weight = 1
next = '/features/proxy/quickstart/'
+++

This page provides a conceptual overview of the Memcached built-in proxy. For a hands-on quick-start guide to install and run the proxy, see [Built-in proxy quickstart]({{<proxy_base_path>}}quickstart).

## Overview {#overview}

Starting with version 1.6.23, Memcached includes a built-in proxy. You can run a Memcached server that acts as a frontend proxy to any number of backend Memcached servers. These backends can run anywhere that the proxy can connect to using TCP/IP.

With a proxied Memcached setup, clients always connect to the Memcached proxy, instead of connecting directly to the backend Memcached servers. The proxy forwards cache requests to pools of backends that you define. This setup can provide improved load balancing and fault tolerance to applications with high-demand caching needs.

## Key concepts {#concepts}

<dl>
<dt>Proxy</dt>
<dd>
The Memcached <em>built-in proxy</em> runs as a frontend interface between client applications that need to read or write cached data, and the underlying backend Memcached servers. The proxy supports <a href="/protocols/basic">the Memcached basic command protocol</a>. This lets clients connect to a Memcached proxy and interact with it using the same techniques that they use with an ordinary Memcached server.
</dd>

<dt>Backend</dt>
<dd>
In the context of a proxied Memcached setup, a <em>backend</em> is a Memcached server that the proxy uses as an underlying cache store.

Setting up a backend doesn't require any special Memcached configuration. A backend is just an ordinary Memcached server that the proxy can connect to using TCP/IP.
</dd>

<dt>Pool</dt>
<dd>
The proxy configuration organizes backends into one or more named groups called <em>pools</em>. Each pool contains one or more backends.

When the proxy receives a request to read or write cache data under a given key, the proxy uses various criteria to select one of its pools. It then forwards the cache request to a backend within that pool. For more information, see [Routing criteria](#routing).

Once it has selected a pool to route a request to, the proxy chooses a single backend within the pool based on a consistent hash of the request key. In this way, a given key is always handled by the same backend within a given pool, for as long as that backend exists within that pool. You can fine-tune the specifics of this hashing behavior, as well as other pool-specific settings, when [configuring your pools]({{<proxy_base_path>}}configure#pools).
</dd>

<dt>Set</dt>
<dd>

More complex routing topologies might require another layer of grouping. You can further group pools into <em>sets</em>. For more information, see [Gather related pools into sets]({{<proxy_base_path>}}configure#sets).

Sets can be useful for gathering pools into named <em>zones</em>, in order to set up failover priorities. For more information, see [Prioritize a route using local zones]({{<proxy_base_path>}}configure#zones).
</dd>

<dt>Route</dt>
<dd>
The core elements of the proxy's configuration are pools and <em>routes</em>. Each route defines the following:

* A mapping between some contextual criteria around a received Memcached command and a pool of backends. For example, a route might map all keys whose key has the prefix `cust/` to the pool named `customer_pool`.

* A _route handler_, one of several defined by the route library. Each route handler tells the proxy which backend pools to forward a request to, and in what style or order. For example, given a list of backend pools, different route handlers can forward requests to those pools either in parallel or serially, and optionally randomize the list first.

For more information on defining pools and routes, see [Configure the built-in proxy]({{<proxy_base_path>}}configure).
</dd>

<dt>Route library</dt>
<dd>
The Memcached project includes a <em>route library</em> file that you can download separately. The route library is a Lua source file that provides basic routing functions that let you define pools, routes, and route handlers.

In typical proxy use, you let the proxy load the route library file as-is, with no changes. In more complex cases, you can freely modify or replace this library file using your own Lua code.
</dd>
</dl>

## Routing criteria {#routing}

The Memcached proxy uses various criteria to decide which of its known backends that it should forward data storage or retrieval requests to. These criteria can include the following:

* **Key prefix.** For example, you can configure the router to send requests for the key `data/12345` to one pool of backend servers, and requests for the key `cust/12345` to a different pool. Requests with no prefix or an unrecognized prefix can be handled by a designated default pool.

    You can configure the proxy to recognize various shapes of prefixes, with different lead-in or separator characters between the prefix and the main key name. For example, if your application's cache keys look more like `--cust//12345` than `cust/12345`, you can still configure the proxy to recognize the former case as having the prefix `cust`.

* **TCP port.** For example, you can configure the proxy to listen on TCP ports `11211` and `11212`, and route requests differently depending upon the connected port.

* **Route handler arguments.** For example, say that you configure the proxy to call the route handler `route_allsync{}`, with a list of three pools, every time it receives a cache request for a key with the prefix `cust`. In that case, a request to store data under `cust/12345-abcd` will be sent in parallel to all three pools that you named in the `route_allsync{}` call in the proxy configuration file, in the definition of the `cust` route.

For more information about configuring proxy pools and routes, see [Configure the built-in proxy]({{<proxy_base_path>}}configure).

## Proxy installation {#installation}

Memcached lets you use its built-in proxy features only if you build the Memcached binary with those features enabled. Proxy features aren't enabled by default. For this reason, using the proxy requires you to compile Memcached from source, rather than installing it from a package manager.

For more information, see [Install the built-in proxy]({{<proxy_base_path>}}install).

## Proxy configuration {#configuration}

If you use the standard route library with the Memcached built-in proxy, then you need to create an additional configuration file that defines the pools available to your proxy, and how it should map requests to pools depending upon various criteria. This configuration file also uses Lua syntax, but in most cases you can treat it as a limited vocabulary specific to Memcached proxy configuration. For more information, see [Configure the built-in proxy]({{<proxy_base_path>}}configure).

In advanced use cases, you can modify the route library file, or even replace it entirely using your own Lua code. This requires a deeper knowledge of the proxy configuration API. For more information, [Configuration API]({{<legacy_proxy_base_path>}}#configuration_api).

## Proxy deployment examples

For a selection of example proxy use cases, see [Usage examples]({{<legacy_proxy_base_path>}}examples).

For a example proxy configuration files, see [Example configuration files]({{<proxy_base_path>}}configure#examples).
