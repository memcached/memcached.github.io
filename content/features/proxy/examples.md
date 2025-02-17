+++
title = 'Configuration Examples'
weight = 35
+++

This page includes configuration files for common use cases.

For several more examples demonstrating various circumstances and topologies, see [the `examples` directory in the route library source](https://github.com/memcached/memcached-proxylibs/tree/main/lib/routelib/examples)

## Basic consistent hashing to pool of servers

Minimal configuration.
No high level routing: just hash keys against the pool's list of backends.
This should be equivalent to a standard memcached client.

```lua
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

## Basic replication for availability or speed

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

## Prefix router

In this example we logically split our cache where by default keys are
spread across a pool of servers to maximize available memory. Then, a small
set of highly accessed "hot" keys are replicated across multiple servers

```lua
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

## Command map

If you don't want to route based on prefix, but instead just based on the
command used, replace map with cmap when building routes{}

```lua
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

## Route by listener port with tags

It's possible to have different route trees based on the listener port.
this is useful if you want to route to different pools by port alone, or
have different prefix trees for different services.

memcached must be started with options like:
-l 127.0.0.1:12051 -l tag_b_:127.0.0.1:12052 -l tag_cccc_:127.0.0.1:12053
this gives a default listener on 12051, "b" tag for 12052, and "cccc" tag
for 12053.

```lua
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

## Nested route handlers

A route handler can accept another route handler for any child type entry.
This lets you freely compose complex behaviors from simpler route handlers.

```lua
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

## Request logging

Results of requests to backend pools can be selectively enabled in the
configuration. Logs can then be viewed by connecting and running `watch
proxyreqs` - Log lines are not generated internally a connection is reading
them, but it is still recommended to configure the `rate` option or other
constraints.

Logs look like: `ts=time gid=num type=proxy_req elapsed=us type=num
code=num status=num res=num cfd=fdnum be=host:port detail=tag req=get key\n`

```lua
settings{
    backend_options = {
        log = {
            rate = 5, -- log one out of every 5 by random chance
            errors = true, -- log all errors
            deadline = 250, -- log all results slower than 250ms
            tag = "all" -- informational tag for log
        }
    }
}

pools{
    main = {
        -- per-pool logging options.
        --[[backend_options = {
            -- if no constraints given, log everything (SLOW!)
            log = { tag = "mainpool" }
        },--]]
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

## Gutter failover for downed backends

This is an example for reducing the impact of a downed cache server.

For example, we have a list of three cache servers in a pool: a, b, c
If cache node 'b' fails, clients will receive errors until the 'b' server
is replaced.

With this example, we use the failover and ttl adjusting routes to allow
temporarily rerouting cache requests while a server is failed. We do this first by "failing over" the request from the bad server to another server, with several optional approaches.

Next, if a request has failed over, we adjust the TTL of any 'set' commands
to be lower. In this example we use five minutes. This prevents cache
entires that have "failed over" from staying around for a long period of
time, which can cause issues if server 'b' repeatedly fails.

The short cache time should allow a good hit rate for objects which are
immediately being reused (ie; a user browsing around a website, or rate
limit counters).

Q&A:
- Do we need to copy all deletes to the gutter pool?
  - If a sufficiently low TTL is used and servers do not typically flap,
  no. Depending on your setup you might have to.
- What about touch/gat commands?
  - This will depend on your needs. Please contact us if you have questions
  here.
- How do we handle backends that are flapping (ie; sometimes timing out but
  not competely dead?
  - You can adjust "anti flap" settings, which will force a backend to
  stay down with a backoff algorithm:
```lua
settings{
  backend_flap_time = 30, -- stable for 30 seconds means not flapping
  backend_flap_backoff_ramp = 1.2, -- multipler for backoff wait time
  backend_flap_backoff_max = 3600, -- try at least once an hour
}
```

Gutter example config:

```lua
local be_list = {
    "127.0.0.1:11214",
    "127.0.0.1:11215",
    "127.0.0.1:11216",
}

pools{
    main = {
        backends = be_list,
    },
    -- for one example, we use a dedicated gutter pool. This server will be
    -- idle unless another server has failed or is being serviced.
    gutter = {
        backends = { "127.1.0.0.1:11311" }
    },
    -- in this example, we reuse the main set of backends, but change the key
    -- hashing seed so keys will map to the list of backends differently. This
    -- can minimize server maintenance while avoiding having idle "gutter"
    -- servers.
    -- The downside is keys may map to the same place, especially if you have
    -- a small list of backends. This can be partly mitigated by having
    -- multiple gutters with different seeds and failing several times.
    -- There may still be keys that fail but they will hopefully be few and
    -- the service can survive until the cache node is restored.
    --
    -- To use this, replace "gutter" below with "gutter_same"
    gutter_same = {
        options = { seed = "failover" },
        backends = be_list,
    },
}

routes{
    cmap = {
        set = route_failover{
            children = { "main", route_ttl{ ttl = 300, child = "gutter" } },
            stats = true,
            miss = false, -- do not fail over on miss, only errors!
            -- do not shuffle the pool list, either!
        },
        -- repeat this for add, cas, ms, as necessary
    },
    -- we handle the rest of the commands with a default failover to gutter.
    default = route_failover{
        children = { "main", "gutter" },
    },
}
```
