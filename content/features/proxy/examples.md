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
pools{
    -- We create an anonymous set of 3 pools, each with one server.
    -- This lets us seamlessly expand the total size of the cache by adding
    -- servers to each pool without changing anything else.
    set_main = {
        { backends = { "127.1.0.1:11211" } },
        { backends = { "127.1.0.2:11211" } },
        { backends = { "127.1.0.3:11211" } },
    },
}

routes{
    cmap = {
        -- override specific commands to fetch from just one server for
        -- performance reasons.
        get = route_failover{
            children = "set_main",
            stats = true, -- get extra stats counters in `stats proxy`
            miss = true, -- failover if miss, not just for down server
            shuffle = true, -- each proxy will have a different order
        },
        -- if gat/gets/mg/etc are used override here.
    },
    -- by default, copy commands to all servers
    default = route_allfastest{
        child = "main",
    },
}
```

### Prefix router

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

### Command map

```lua
-- if you don't want to route based on prefix, but instead just based on the
-- command used, replace map with cmap when building routes{}
pools{
    foo = {
        backends = {
            "127.0.0.1:11214",
            "127.0.0.1:11215",
        }
    },
    bar = {
        backends = {
            "127.0.0.1:11216",
        }
    },
}

routes{
    cmap = {
        get = route_direct{
            child = "foo",
        },
    },
    default = route_allfastest{
        children = { "bar" }
    },
}
```

### Route by listener port with tags

```lua
-- it's possible to have different route trees based on the listener port.
-- this is useful if you want to route to different pools by port alone, or
-- have different prefix trees for different services.
--
-- memcached must be started like:
-- -l 127.0.0.1:12051 -l tag_b_:127.0.0.1:12052 -l tag_cccc_:127.0.0.1:12053
-- this gives a default listener on 12051, "b" tag for 12052, and "cccc" tag
-- for 12053.
pools{
    foo = {
        backends = {
            "127.0.0.1:11214",
        }
    },
    bar = {
        backends = {
            { host = "127.0.0.1", port = 11215, retrytimeout = 5 }
        }
    },
    baz = {
        backends = {
            "127.0.0.1:11216",
        }
    },
}

-- no supplied tag makes this the "default" router.
routes{
    default = route_direct{
        child = "foo",
    }
}

-- this route tree is only executed if a client is connected to port 12052
routes{
    tag = "b",
    default = route_direct{
        child = "bar"
    }
}

-- this route tree is only executed if a client is connected to port 12053
routes{
    tag = "cccc",
    default = route_direct{
        child = "baz"
    }
}
```

### Nested route handlers

```lua
-- A route handler can accept another route handler for any child type entry.
-- This lets you freely compose complex behaviors from simpler route handlers.
pools{
    one = {
        backends = {
            "127.0.0.1:11214",
            "127.0.0.1:11215",
        }
    },
    two = {
        backends = {
            "127.0.0.1:11216",
        }
    },
    three = {
        backends = {
            "127.0.0.1:11217",
        }
    },
}

routes{
    map = {
        foo = route_split{
            child_a = "foo",
            child_b = route_allfastest{
                children = { "two", "three" }
            },
        },
    },
}
```
