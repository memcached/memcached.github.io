+++
title = 'Run the built-in proxy'
weight = 40
+++

This page is about running Memcached as a proxy for other Memcached servers.

For a general overview of the proxy, see [Proxy]({{<legacy_proxy_base_path>}}). For a hands-on tutorial that guides you through compiling and running the proxy with a simple configuration, see [Built-in Proxy Quickstart]({{<proxy_base_path>}}quickstart).

## Before you begin {#prereqs}

Before you can run the proxy, you need the following:

* Memcached, with proxy features enabled, installed on your system.
* The proxy route library file downloaded to some path on your file system.
* A proxy configuration file that you created.

For more information, see [Install the built-in proxy]({{<proxy_base_path>}}install) and
[Configure the built-in proxy]({{<proxy_base_path>}}configure).

## Run the proxy

{{< callout type="info" >}}This section assumes that you installed your proxy-enabled Memcached to have the default command name `memcached`. If you configured the build process to name the command something else—for example, `memcached-proxy`—then use that name here instead.{{</callout>}}

To run the proxy, launch the proxy-enabled Memcached binary with the `proxy_config` and `proxy_arg` command-line options:

```
memcached -o proxy_config={{<var>}}ROUTE_LIBRARY_PATH{{</var>}},proxy_arg={{<var>}}CONFIGURATION_PATH{{</var>}} {{<var>}}FURTHER_ARGUMENTS{{</var>}}
```

Replace the following:

* <var>ROUTE_LIBRARY_PATH</var>: the file system path to the proxy route library file.
* <var>CONFIGURATION_PATH</var>: the file system path to your own proxy configuration file.
* <var>FURTHER_ARGUMENTS</var>: additional Memcached command-line flags. For a complete list, run `memcached --help`.

To run the proxy in the background, append a `&` to the end of the command, in the usual Unix fashion.

### An example command

The following example command launches a Memcached proxy with the following aspects:

* The proxy loads a route library file named `routelib.lua` stored in your home directory.
* The proxy loads a configuration file named `my_config.lua` stored in your home directory.
* The proxy listens for connections on TCP port `11212`.
* The proxy runs in the background.

```
memcached -o proxy_config=$HOME/routelib.lua,proxy_arg=$HOME/my_config.lua -p 11212 &
```

### Test the connection

Once it's running, the Memcached proxy presents a network interface very similar to an ordinary Memcached server. To test that it's up and listening on the port that you expect, use `telnet` to connect to that port, and try a basic command from the Memcached protocol.

For example, if you are running a proxy on port 11211, you can connect to the proxy directly with this command:

```
telnet 11211
```

If Memcached is running at that port, then the `telnet` program responds similar to the following:

```
Trying ::1...
Connected to localhost.
Escape character is '^]'.
```

Request some data under an arbitrary key name such as `proxy-test`, which need not correspond to any key known by your Memcached nodes:

```
get proxy-test
```

Memcached replies with the stored data under that key, if there is any, followed by `END`.

To exit this test connection follow these steps:

1. Press <kbd>Ctrl</kbd> + <kbd>]</kbd>.

1. At the `telnet>` prompt, enter `quit`.

## Run the proxy with multiple tagged TCP ports {#tags}

You can run optionally the proxy to listen on multiple TCP ports, and assign a short text-string _tag_ to each one. This lets you define multiple complete route trees in the proxy configuration file, where the proxy handles a request differently depending upon the port that the requesting client is connected to.

To specify multiple, tagged TCP ports, include the `-l` or `--listen` command-line flag once for every port, with the syntax `TAG:HOST:PORT`. You can mix untagged ports with tagged ones.

For example, the following command runs the proxy with three TCP ports active. Two of the ports are tagged `main` and `alt`, respectively, and one port has no tag associated with it:

```
memcached -o proxy_config=$HOME/routelib.lua,proxy_arg=$HOME/my_config.lua -l 127.0.0.1:11211 -l main:127.0.0.1:11212 -l alt:127.0.0.1:11213 &
```

For more information on configuring the proxy to handle requests based on connected ports, see [Define multiple route trees]({{<proxy_base_path>}}configure/#multiple-trees).

## View proxy statistics {#stats}

To view statistics that a Memcached process has collected, send the `stats proxy` command to the proxy.

The output resembles the following:

```
STAT active_req_limit 0
STAT buffer_memory_limit 0
STAT buffer_memory_used 0
STAT vm_gc_runs 3
STAT vm_memory_kb 384
STAT cmd_mg 0
STAT cmd_ms 0
STAT cmd_md 0
STAT cmd_mn 0
STAT cmd_ma 0
STAT cmd_me 0
STAT cmd_get 2
STAT cmd_gat 0
STAT cmd_set 0
STAT cmd_add 0
STAT cmd_cas 0
STAT cmd_gets 0
STAT cmd_gats 0
STAT cmd_incr 0
STAT cmd_decr 0
STAT cmd_touch 0
STAT cmd_append 0
STAT cmd_prepend 0
STAT cmd_delete 0
STAT cmd_replace 0
END
```

## Log proxy events {#log}

To capture a log of Memcached events that are specific to the proxy, send the `watch proxyevents` command to the proxy.

The proxy responds with `OK`. For the remainder of that connection, the proxy streams a log of any proxy-related event that occurs.

For example, if you run `watch proxyevents` and then a backend server unexpectedly goes down, you might see a number of messages like the following:

```
ts=1726602194.226721 gid=4 type=proxy_backend error=disconnected name=127.0.0.1 port=11213 label=127.0.0.1:11213 depth=0 rbuf=
ts=1726602194.227476 gid=11 type=proxy_backend error=reading name=127.0.0.1 port=11213 label=127.0.0.1:11213 depth=0 rbuf=
ts=1726602194.227681 gid=12 type=proxy_backend error=connecting name=127.0.0.1 port=11213 label=127.0.0.1:11213 depth=0 rbuf=
ts=1726602194.227708 gid=13 type=proxy_backend error=connecting name=127.0.0.1 port=11213 label=127.0.0.1:11213 depth=0 rbuf=
ts=1726602194.227890 gid=14 type=proxy_backend error=markedbad name=127.0.0.1 port=11213 label=127.0.0.1:11213 retry=3
ts=1726602194.226510 gid=3 type=proxy_backend error=disconnected name=127.0.0.1 port=11213 label=127.0.0.1:11213 depth=0 rbuf=
```
