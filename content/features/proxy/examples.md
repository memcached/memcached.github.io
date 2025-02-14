+++
title = 'Configuration Examples'
weight = 35
+++

This page includes configuration files for common use cases.

For several more examples demonstrating various circumstances and topologies, see [the `examples` directory in the route library source](https://github.com/memcached/memcached-proxylibs/tree/main/lib/routelib/examples)

### Basic consistent hashing to pool of servers

```lua
-- Minimal configuration.
-- No high level routing: just hash keys against the pool's list of backends.
-- This should be equivalent to a standard memcached client.

pools{
    main = {
        backends = {
            "127.0.0.1:11214",
            "127.0.0.1:11215",
        }
    }
}

routes{
    default = route_direct{ child = "main" }
}
```

### Basic replication for availability or speed

Please see
[REPLICATE.md](https://github.com/memcached/memcached-proxylibs/blob/main/lib/routelib/REPLICATE.md)
for more details on what replication means in memcached.

```lua
-- In this example we logically split our cache where by default keys are
-- spread across a pool of servers to maximize available memory. Then, a small
-- set of highly accessed "hot" keys are replicated across multiple servers

pools{
    main = {
        backends = {
            "127.0.0.1:11211",
            "127.0.0.2:11211",
            "127.0.0.3:11211",
            "127.0.0.4:11211",
            "127.0.0.5:11211",
        }
    },
    -- These could even be a subset of the same servers listed above, however
    -- we have to describe it as three pools with one backend in each.
    set_hot = {
        { backends = { "127.1.0.1:11211" } },
        { backends = { "127.1.0.2:11211" } },
        { backends = { "127.1.0.3:11211" } },
    },
}

-- keys with prefix "hot/" get routed to a special handler.
routes{
    map = {
        hot = cmdmap{
            -- send all commands to all pools by default
            -- only wait for one response before returning result
            all = route_allfastest{
                children = "set_hot" 
            },
            -- override specific commands to fetch from just one server for
            -- performance reasons.
            get = route_failover{
                children = "set_hot",
                stats = true,
                miss = true, -- failover if miss, not just for down server
                shuffle = true, -- each proxy will have a different order
            },
            -- if gat/gets/mg/etc are used override here.
        },
    },
    -- by default, all commands map to exactly one server within the larger
    -- pool.
    default = route_direct{
        child = "main",
    },
}
```
