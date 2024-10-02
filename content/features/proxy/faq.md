+++
title = 'Frequently asked questions'
weight = 99
+++

For a general overview of the built-in proxy, see [Built-in proxy]({{<proxy_base_path>}}).

## Why Lua?

I thought ya'll could use a break from YAML :) We use Lua and its
extensibility like glue: nearly everyone has different methods of managing
their configuration, and in most cases a simple lua script would be able to
parse output from such services or talk to them directly. A handful of for
loops and object reuses can remove tens of thousands of lines of generated
configuration.

For route handling our usage of Lua is more bold, but again it's simply glue.
Configurations for routes will tend to be wide but shallow: just a couple
small function calls based on the contents of the request, and then the
request is handed back off to the C side. Object methods written in C further
avoid copying strings/data to/from Lua for common operations.

Since the proxy integrates with memcached's existing systems, we can (over
time) ensure all large memory allocations are handled outside of Lua. The rest
of the tiny allocations, if any, are tied to a coroutine and released
immediately after the (typically under a millisecond) request/response.

## Why not LuaJIT?

For half the answer to this, please see [Why lua?](#why-lua) - for loading
configuration the performance difference isn't going to matter much. For route
handling very little work is done from Lua, which will be reduced or removed
as development continues.

When development started LuaJIT had a much more rocky status. Also Lua 5.4 has
a good number of language and performance improvements on its own.

## Why not use a mesh router?

Memcached's proxy is not intended to replace a mesh router; its scope is much
smaller and more performance focused. A mesh router may be highly configurable,
with broad support, but will be very slow. Caching services (and in this case
a caching proxy) can be used to restore performance to a service migrated to a
mesh router; for cost or practicality reasons.

Many data queries don't or shouldn't go through a mesh router anyway. If you
want to speed up access to data storage from an application implemented as an
endpoint on a mesh router, a caching service is what sits behind that
endpoint but before its actual data storage.

## What happens to in-flight requests on config reload?

When the configuration is reloaded, `mcp_config_routes` gets called again.
During this process routes are assembled into functions and `mcp.attach()` is
called with these top level hooks. Any time a request comes in these top level
hooks are checked for their associated function.

This means once a request fires off its hook, it is immune from configuration
changes until it completes. Any new request coming in will see what's
presently configured in a hook and get the new code even as older requests are
being processed.

This does mean you have to be careful with using global variables in Lua: we
suggest the use of function closures for routes to avoid configurations
clashing.
