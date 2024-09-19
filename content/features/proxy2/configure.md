+++
title = 'Configure the built-in proxy'
weight = 30
+++

This page is about configuring and running the Memcached built-in proxy.

For a general overview of the proxy, see [Proxy]({{<legacy_proxy_base_path>}}). For a hands-on tutorial that guides you through compiling and running the proxy with a simple configuration, see [Built-in Proxy Quickstart]({{<proxy_base_path>}}quickstart).

## Before you begin {#prereqs}

Before you and configure and run the proxy, you need the following:

* Memcached, with proxy features enabled, installed on your system.
* The proxy route library file downloaded to some path on your file system.

For more information, see [Install the built-in proxy]({{<proxy_base_path>}}install).

## Create a proxy configuration file {#create}

Before you can run the proxy, you need to create a configuration that lists the pools of Memcached servers that you want to use as the proxy's back end, and describes the routing trees that you want the proxy to use.

The configuration file is a Lua script that extends the standard proxy route library. Advanced users can modify the route library itself, or even replace it entirely with their own Lua code. The techniques described on this page focus on using the route library as-is, which covers the majority of Memcached proxy use-cases.

### Overview of the configuration file

The configuration file has four general parts:

1. Optional invocations of global functions to turn on logging or debugging features.
1. An optional `settings{}` block, which sets various proxy settings to values other than their defaults.
1. A `pools{}` block, which defines groups of backend Memcached servers that the proxy routes queries to.
1. One or more `routes{}` blocks, which lay out the rules by which the proxy routes requests to the pools defined in the `pools{}` blocks.

    It's common to have only one `routes{}` block. If you want to have the router handle requests differently depending upon connection context, [you can define multiple `routes{}` blocks](#multiple-trees).

The following sections detail the contents of each of the above parts, with examples.

### Enable logging and debugging

To enable logging and debugging features of the proxy, include any of the following function calls at the top of the configuration file:

* `verbose(1)` has Memcached print additional information to standard output when loading the proxy configuration. This setting also lets you use the `say({{<var>}}TEXT{{</var>}})` function to print <var>TEXT</var> to standard output while loading your custom configuration.

* `debug(1)` turns on a debugging mode that is mainly useful if you are modifying the routing library. This setting also lets you use the `dsay({{<var>}}TEXT{{</var>}})` function to print <var>TEXT</var> to standard output while loading the proxy configuration.

### Adjust proxy settings

To fine-tune various proxy behaviors, include a `settings{}` block in your configuration file. The block contains any number of function calls that set values on the `mcp` object. For the full list of available setting functions, see [General API documentation]({{<legacy_proxy_base_path>}}#general-api-documentation).

For example, the following settings block sets a ten-second connection timeout, and an upper limit of five active requests:

```lua
settings {
    mcp.backend_connect_timeout(10)
    mcp.active_req_limit(5)
}
```

### Define backend pools

Use the required `pools{}` block to define the locations and other attributes of the back-end Memcached servers that the router directs queries to.

The `pools{}` block contains one or more pool definitions in the following format:

```lua
{{<var>}}POOL_NAME{{</var>}} = {
    backend_options = { {{<var>}}BACKEND_OPTIONS{{</var>}} },
    backends = {
        "{{<var>}}HOST{{</var>}}:{{<var>}}PORT{{</var>}}",
        {{<var>}}[ ... ]{{</var>}}
    }
}
```

Replace the following:

* <var>POOL_NAME</var>: a name for this pool—for example, `northeast_1`.
* <var>BACKEND_OPTIONS</var>: (optional) a comma-separated list, in `key=value` format, of settings for the proxy to apply to all of the backends in the pool. The proxy applies for the default values for any settings not defined here. For a list of settings, see [Backend API]({{<legacy_proxy_base_path>}}/Proxy#backend-api).
* <var>HOST</var>: the IP address or hostname of the Memcached server you are defining as a backend.
* <var>PORT</var>: (optional) the TCP port of the Memcached server you are defining as a backend. The default value is `11211`.

To fine-tune the proxy settings for an individual backend, replace its entry in the `backends` list with a curly-braced list of back-end options. The list must include `host`.

The following example defines two backends, including one that runs on port 11212 and has a ten-second connection timeout:

```lua
backends = {
    "127.0.0.1:11211",   
    { host = "127.0.0.1", port = 11212, connecttimeout = 10 },
}
```

You can optionally gather pools into a named _set_, which contains a list of pool definitions. You might want to employ this technique if you have several pools of backends that you want the router to treat as mutually equivalent with synchronized data:

```lua
{{<var>}}SET_NAME{{</var>}} = {
    {
        { backends = {{<var>}}[ ... ]{{</var>}} },
        { backends = {{<var>}}[ ... ]{{</var>}} },
        {{<var>}}[ ... ]{{</var>}}
    },
    {{<var>}}[ ... ]{{</var>}}
}
```

Replace <var>SET_NAME</var> with a name for this set of pools.

The following example `pools{}` block defines two pools with three backends each:

```lua
pools {
    main_pool = {
        backend_options = { connecttimeout = 5 },
        backends = {
            "192.0.2.1",
            "192.0.2.2",
            "192.0.2.3:11212",
        }
    },
    customer_pool = {
        backends = {
            "203.0.113.1",
            "203.0.113.10",
            { host = "203.0.113.20", port = 11212, connecttimeout = 10 },
        },
    },
}
```

### Define proxy routes

Use the required `routes{}` block to define how the proxy should handle data storage or retrieval queries. You do this by mapping a set of key-prefix strings to the pools of backend servers that you defined in the `pools{}` block.

For example, if you have defined a pool of Memcached nodes under the pool name `main_pool`, then you can define a route that redirects queries under keys like `main/key-12345` and `main/another-key-321` to that pool's backend servers to store or retrieve data under those keys.

{{< callout type="info" >}}Pool names and key prefixes don't need to be similar in the way that `main_pool` and `main/key-12345` are. However, it can be good practice to give pools and their corresponding key prefixes similar names, for the sake of clarity.{{</callout>}}

The `routes{}` block has the following format:

```lua
-- Optional: instruct the proxy how to parse key prefixes
conf = {
    mode = "{{<var>}}MODE{{</var>}}",
    start = "{{<var>}}START_STRING{{</var>}}",
    stop = "{{<var>}}STOP_STRING{{</var>}}",
},

-- Define one route handler for every key prefix that requires non-default
-- handling
map = {
    {{<var>}}KEY_PREFIX{{</var>}} = {{<var>}}HANDLER_TYPE{{</var>}}{ {{<var>}}HANDLER_ARGS{{</var>}} },
    {{<var>}}[ ... ]{{</var>}} 
},

-- Define one default router handler, as a catch-all for any prefixes not matched
-- in the map section, as well as keys with no prefixes at all
default = {{<var>}}HANDLER_TYPE{{</var>}}{ {{<var>}}HANDLER_ARGS{{</var>}} },
```

Replace the following:

* <var>MODE</var>: (optional) the technique that the proxy applies to determine the prefix of each key. The value must be one of the following:

    * `prefix`: The prefix is every character from the start of the key until the characters defined by the separate `stop` argument. This is the default.
    
    * `anchor`: The prefix is every character between the two substrings defined by the separate `start` and `stop` arguments.
    
* <var>START_STRING</var>: if the mode is `anchor`, then this is a substring of five or fewer characters at the start of every key name that precedes the prefix.

* <var>STOP_STRING</var>: a substring that separates the prefix from the remainder of the key name.

* <var>KEY_PREFIX</var>: the key prefix to map to this route handler.
    
* <var>HANDLER_TYPE</var>: the name of a route library handler. For a list of all of the handlers available, see [Route handler reference]({{<proxy_base_path>}}reference).

* <var>HANDLER_ARGS</var>: the arguments to pass to the route handler. Typically, this is where you name one or more pools. The list of arguments and their expected syntax depends on the handler type. For more information, see [Route handler reference]({{<proxy_base_path>}}reference).

### Example configuration files

The following example file demonstrates all four of the configuration file sections described earlier on this page:

```lua
-- Turn on the proxy's verbose mode.
verbose(1)

-- Override a handful of default settings for proxy behavior.
settings {
    active_request_limit = 100,
    pool_options = {
        filter = "tags",
        filter_conf = "{}"
    }
}

-- Define two backend pools of Memcached servers. Each pool has three nodes.
pools {
    main_pool = {
        backend_options = { connecttimeout = 5 },
        backends = {
            "192.0.2.1",
            "192.0.2.2",
            "192.0.2.3:11212",
        }
    },
    customer_pool = {
        backends = {
            "203.0.113.1",
            "203.0.113.10",
            { host = "203.0.113.20", port = 11212, connecttimeout = 10 },
        },
    },
}

-- Define two routes, each of which maps to one of the pools defined earliers.
-- And then define a default catch-all route.
routes {
    map = {
        -- Handle keys that start with "main/"
        main = route_allfastest {
            children = { "main_pool" },
        },
        cust = cmdmap{
            -- Handle only SET commands for keys with the prefix with "cust"
            set = route_direct {
                child = { "customer_pool" },
            },
        },
    },

    -- Handle keys that don't have a mapped prefix.
    default = route_direct {
        child = { "main_pool" }
    },
}
```

This example also demonstrates how you can add comments, using [Lua comment syntax](https://www.codecademy.com/resources/docs/lua/comments), to your proxy configuration file.

For several more examples demonstrating you can configure the proxy to handle various circumstances and topologies, see [the `examples` directory in the route library source](https://github.com/memcached/memcached-proxylibs/tree/main/lib/routelib/examples).

## Mark a backend as down {#down}

You can modify your proxy configuration file to mark a backend server as
being down. This action tells the proxy that the backend still exists, but is temporarily unavailable. Marking a backend as down has two effects:

* The proxy doesn't route any queries to the backend marked as down.
* The proxy doesn't remap any keys already stored on the backend.

To mark a backend as down, add the `_down_` keyword to its definition in the configuration file's `pools{}` block. The following example pool definition has one server marked as down:

```lua
    main_pool = {
        backends = {
            "192.0.2.1",
            "192.0.2.2 _down_",
            "192.0.2.3:11212",
        }
    },
```

If you have defined the backend using the alternate list-based syntax, then add `down=true` instead:

```lua
    customer_pool = {
        backends = {
            "203.0.113.1",
            "203.0.113.10",
            { host="203.0.113.20", port=11212, down=true, flaptime=10 },
        },
    },
```

To mark the backend as online again, remove the `_down_` or `down=true` attribute from the backend's definition in the proxy configuration file.

## Prioritize a route using local zones {#zones}

If you have organized your pools into named sets, then you can further configure the proxy to prefer routing requests to certain pools within a set, even if other pools are available. You might want to do this to prefer routing requests to a pool that is geographically closer to the proxy, for example.

Doing this involves the following steps:

1. Declare a _local zone_ in your proxy configuration file.
1. Attach zone labels to the pools within your set definitions.
1. In your route definitions, invoke a route handler that makes use of zone information. The only such handler defined by the standard route library is `route_zfailover`.

To declare the local zone and attach zone labels to your defined pools, modify your configuration file's `pools{}` block like this:

```lua
local_zone("{{<var>}}LOCAL_ZONE_NAME{{</var>}}")

pools {
    {{<var>}}SET_NAME{{</var>}} = {
    {
        {{<var>}}POOL_ZONE_NAME{{</var>}} = { backends = {{<var>}}[ ... ]{{</var>}} },
        {{<var>}}[ ... ]{{</var>}}
    },
    {{<var>}}[ ... ]{{</var>}}
}
```

Replace the following:
* <var>LOCAL_ZONE_NAME</var>: the name of the zone that this proxy prefers to route requests to.
* <var>POOL_ZONE_NAME</var>: the name of the zone that this pool is in.

The following example excerpt from a proxy configuration file that declares a local zone—in this case, "`west`"—and then defines two sets of zoned pools.

```lua
local_zone("west")

pools {
    main_pool_set = {
        west   = {
            backends = {
                "192.0.2.1",
                "192.0.2.2",
                "192.0.2.3:11212",
            },
        south  = {
            backends = {
                {{<var>}}[ ... ]{{</var>}}
            },
        east   = {
            backends = { 
                {{<var>}}[ ... ]{{</var>}}
            } 
        },
    },
    
    customer_pool_set = {
        west   = {
            backends = {
                "203.0.113.1",
                "203.0.113.10",
                { host="203.0.113.20", port=11212, flaptime=10 },
            },
        south  = {
            backends = {
                {{<var>}}[ ... ]{{</var>}}
            },
        east   = {
            backends = { 
                {{<var>}}[ ... ]{{</var>}}
            } 
        },
    },
}
```

The only route handler that makes use of zone information is [`route_zfailover`]({{<proxy_base_path>}}reference#route_zfailover). The following proxy configuration excerpt extends the previous example with a `routes{}` block that uses `route_zfailover`.

```lua
routes {
    map = {
        main = route_zfailover {
            children = "main_pool_set",
        },
        cust = cmdmap{
            set = route_zfailover {
                children = "customer_pool_set",
            },
        },
    },

    default = route_allfastest {
        children = { "main_pool_set" }
    },
}
```

## Define multiple route trees {#multiple-trees}

You can configure the proxy to have more than one tree of route handlers, each with their own, separate `routes{}` block in the configuration file. The proxy chooses which `routes{}` block to apply to a given request depending on the TCP port that the request arrived on.

To define multiple route trees, follow these steps:

1. Define one `routes{}` block for each route tree. Within each block, define a `tag` that is unique to that block. You can optionally leave one `routes{}` block with no tag, which marks it as the default tree. The following example defines two tagged route trees named `main` and `alt`, and one default route tree:

    ```lua
    routes {
        tag = "main",
        map { {{<var>}}[ ... ]{{</var>}} },
    }
    
    routes {
        tag = "alt",
        map { {{<var>}}[ ... ]{{</var>}} },
    }
    
    routes {
        map { {{<var>}}[ ... ]{{</var>}} },
    }
    ```

1. Run the proxy so that it listens on one TCP port for every tree that you define. Assign the ports tags that match the tags you specified in your configuration file. Extending the previous example, that would mean running the proxy on at least two ports, tagging one `main` and the other `alt`.

    For more information, see [Run the proxy with multiple tagged TCP ports]({{<proxy_base_path>}}run#tags).

## Reconfigure a running proxy {#reload}

You can update your proxy configuration file without interrupting the proxy service. To do this, follow these steps:

1. Update your configuration file as needed.

1. Send a `SIGHUP` signal to the Memcached proxy:

    ```command
    kill -HUP {{<var>}}PROXY_PROCESS{{</var>}}
    ```

    Replace <var>PROXY_PROCESS</var> with the process ID of the proxy.
    
If the updated configuration file is valid, then Memcached reloads its configuration and continues running. Otherwise, Memcached exits with an error.
