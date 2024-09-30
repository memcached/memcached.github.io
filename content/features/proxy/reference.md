+++
title = 'Route handler reference'
weight = 100
+++

This page is a reference for the route handlers provided by the standard route library of the Memcached built-in proxy.

For a guide to invoking these route handlers in your proxy configuration file, see [Configure the built-in proxy]({{<proxy_base_path>}}configure). For examples, see [Example configuration files]({{<proxy_base_path>}}configure#examples).

## Route handlers

### `route_direct`

Routes the request to a single pool, and returns the result.

```lua
route_direct{
    child = "{{<var>}}POOL_NAME{{</var>}}"
}
```

Replace <var>POOL_NAME</var> with the name of a pool defined in the `pools{}` block of your configuration file.

### `route_allsync`

Routes the request to a list of pools, in parallel.

The proxy waits for all responses to
complete. If the proxy receives an error from any of the pools, then the proxy returns the first error that it received. Otherwise, it returns the last
result that it received.

```lua
route_allsync{
    children = {{<var>}}POOL_LIST_OR_SET_NAME{{</var>}}
}
```
Replace <var>POOL_LIST_OR_SET_NAME</var> with either one of the following:

* A bracketed, comma-separated list of of pool names defined in the `pools{}` block of your configuration file—for example, `{ "cust_pool_1", "cust_pool_2" }`.

* The name of a set defined in the `pools{}` block of your configuration file—for example, `"set_cust_pools"`.


### `route_allfastest`

Routes the request a list of pools, in parallel.

The proxy returns the first response that it receives from any pool.

```lua
route_allfastest{
    children = {{<var>}}POOL_LIST_OR_SET_NAME{{</var>}}
}
```

Replace <var>POOL_LIST_OR_SET_NAME</var> with either one of the following:

* A bracketed, comma-separated list of of pool names defined in the `pools{}` block of your configuration file—for example, `{ "cust_pool_1", "cust_pool_2" }`.

* The name of a set defined in the `pools{}` block of your configuration file—for example, `"set_cust_pools"`.

### `route_split`

Routes a request to exactly two pools.

The proxy waits only for a response from the pool assigned to the argumment `child_a`, and returns the result received from it.

```lua
route_split{
    child_a = "{{<var>}}POOL_NAME_A{{</var>}}"
    child_b = "{{<var>}}POOL_NAME_B{{</var>}}"
}
```

Replace the following:

* <var>POOL_NAME_A</var>: the name of a pool defined in the `pools{}` block of your proxy configuration file.
* <var>POOL_NAME_B</var>: the name of a different pool defined in the `pools{}` block of your proxy configuration file.

### `route_ttl`

Sets the time to live in a `set`, `add`, `cas`, or `ms` request.

For more information about these requests, see [Basic Text Protocol](/protocols/basic/).

```lua
route_ttl{
  ttl = {{<var>}}TTL{{</var>}},
  child = "{{<var>}}POOL_NAME{{</var>}}"
}
```

Replace the following:

* <var>TTL</var>: an integer representing the new TTL value.
* <var>POOL_NAME</var>: the name of a pool defined in the `pools{}` block of your configuration file.

### `route_failover`

Routes the request to a list of pools, serially. You can tune the behavior of this handler by passing in additional arguments.

```lua
route_failover{
    children = {{<var>}}POOL_LIST_OR_SET_NAME{{</var>}},
    failover_count = {{<var>}}FAILOVER_COUNT{{</var>}},
    shuffle = {{<var>}}SHUFFLE_BOOLEAN{{</var>}},
    miss = {{<var>}}MISS_BOOLEAN{{</var>}},
}
```

Replace the following:

* <var>POOL_LIST_OR_SET_NAME</var>: either one of the following:
    * A bracketed, comma-separated list of of pool names defined in the `pools{}` block of your configuration file—for example, `{ "cust_pool_1", "cust_pool_2" }`.
    * The name of a set defined in the `pools{}` block of your configuration file—for example, `"set_cust_pools"`.
* <var>FAILOVER_COUNT</var>: the number of failures that the proxy receives from a pool before fails over to the next pool in the list.
* <var>SHUFFLE_BOOLEAN</var>: if true, then the proxy randomizes the list of pools before routing the request. Otherwise, uses the list of pools in the given order.
* <var>MISS_BOOLEAN</var>: if true, then the proxy treats both misses and errors as failures. Otherwise, the proxy counts only errors as failures.

### `route_zfailover`

Routes the request to a named set of pools. You can tune the behavior of this handler by passing in additional arguments.

To use this handler, you need to define zones in your configuration file. For more information, see [Prioritize a route using local zones]({{<proxy_base_path>}}configure#zones).

```lua
route_zfailover{
    children = "{{<var>}}SET_NAME{{</var>}}",
    stats = true,
    miss = true
}
```

Replace the following:

* <var>SET_NAME</var>: the name of a set of pools defined in the `pools{}` block of your configuration file.
* <var>STATS_BOOLEAN</var>: if true, then Memcached includes a tally of failovers in the response to [the `stats proxy` command]({{<proxy_base_path>}}run/#stats).
* <var>MISS_BOOLEAN</var>: if true, then the proxy treats both errors and missing cache data failures. Otherwise, the proxy counts only errors as failures.

## Restrict route handlers to certain commands {#commands}

By default, the proxy chooses a route for a request regardless of the Memcached command in that request. You can restrict routes to handle only certain Memcached commands by wrapping route handler invocations in a `cmdmap{}` call:

```lua
cmdmap {
    {{<var>}}COMMAND{{</var>}} = {{<var>}}ROUTE_HANDLER{{</var>}}
}
```

Replace the following:

* <var>COMMAND</var>: a Memcached command—for example, `set`. For a command list, see [Basic Text Protocol](/protocols/basic/).
* <var>ROUTE_HANDLER</var>: a full route-handler defnition—for example, `route_direct{ [...] }`.

You can use a `cmdmap{}` call in your configuration file in any place where a route handler call is valid. The following example `map{}` block defines a route that proxy uses only for keys with the prefix `main` and the command `set`:

```lua
    map = {
        main = cmdmap{
            set = route_direct{
                child = "main_pool",
            },
        },
    },
```
