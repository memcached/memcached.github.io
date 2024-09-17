+++
title = 'Built-in proxy quickstart'
date = 2024-09-01T09:30:55-07:00
weight = 10
+++

This page helps you get familiar with the Memcached built-in proxy by guiding you through the following tasks:

* Build Memcached with its proxy features enabled.
* Download the Lua library that the proxy requires for common tasks.
* Set up a minimal proxy configuration file that lets the proxy use two Memcached servers as its backend.
* Run this proxy-based Memcached architecture, and observe the proxy at work by connecting to it and querying it.

For detailed documentation about the Memcached built-in proxy, see [Proxy](({{<legacy_proxy_base_path>}})).

## Before you begin

Make sure that your machine has the necessary development tools and libraries
to build Memcached from source. These requirements are probably available from your operating system's package manager.

For example, on Debian, you can satisfy these requirements by having the following packages installed:

* `gcc`
* `make`
* `libevent-dev`

The steps on this page guide you though launching three Memcached servers on TCP ports `11211`, `11212`, and `11213`. If you already have Memcached services running on those ports and you don't want to disrupt them, then you should take either of these additional steps:

* Substitute different port numbers for this tutorial.
* Run this tutorial as written on a different machine that isn't running Memcached.

## Download and build the proxy

Because the Memcached built-in proxy is a non-default, compile-time configuration option, the proxy is available as a Memcached feature only if you build Memcached from source. To do this, follow these steps:

1. Download the latest Memcached release tarball:

    ```
    wget https://memcached.org/latest
    ```

1. Unzip and untar the tarball, then change your working directory to the source directory:

    ```posix-terminal
    tar xzvf memcached-{{<var>}}VERSION{{</var>}}.tar.gz
    
    cd memcached-{{<var>}}VERSION{{</var>}}
    ```
    
    Replace <var>`VERSION`</var> with the version number in the tarball that you downloaded in the previous step.
    
1. Configure the Memcached build process to enable the built-in proxy:

    ```
    ./configure --with-proxy
    ```
    
1. Build and test Memcached:

    ```
    make
    
    make test
    ```
    
    If you encounter errors related to missing tools or libraries, make sure that your system has the development packages that you need to build Memcached from source. For more information, see "Before you begin", earlier on this page.

    Note: For the sake of simplicity and easier cleanup, this quick-start guide doesn't include a `make install` step. If you want to install the proxy-enabled Memcached on this machine, then you can do as a final step at the end of this guide.

## Download the route library

Run the following command:

```
wget https://raw.githubusercontent.com/memcached/memcached-proxylibs/main/lib/routelib/routelib.lua
```

## Configure the proxy
    
To create a minimal proxy configuration file named `config.lua`, run the following command:

```
echo 'pools{
    quickstart_pool = {
        backends = {
            "127.0.0.1:11212",
        }
    },
    fallback = {
        backends = {
            "127.0.0.1:11213",
        }
    }
}

routes{
    map = {
        quickstart_route = route_direct{
            child = "quickstart_pool",
        },
    },
    default = route_direct{ child = "fallback" }
}' > config.lua
```

## Set up and run the proxied Memcached servers

1. Using the binary that you built in a previous step, launch two ordinary Memcached cache servers, running on TCP ports `11212` and `11213`:

    ```
    ./memcached -l localhost -p 11212 &
    
    ./memcached -l localhost -p 11213 &
    ```
    
1. Launch a third Memcached server in proxy mode, by specifying the proxy-specific configuration files that you downloaded and created, respectively, in previous steps:

    ```
    ./memcached -l localhost -p 11211 -o proxy_config=routelib.lua,proxy_arg=config.lua &
    ```

## Connect to and use the proxy    

1. Connect to the proxy and set a variable:

    ```
    telnet localhost 11211
    set quickstart-data 0 0 6
    Hello!
    ```
    
    The Memcached proxy responds with `STORED`.
    
1. Fetch the data again:

    ```
    get quickstart-data
    ```
    
    The Memcached proxy responds with the following:
    
    ```
    Hello!
    END
    ```

1. Terminate the Telnet session:

    1. Press <kbd>Ctrl</kbd> + <kbd>]</kbd>.
    
    1. At the `telnet>` prompt, enter `quit`.

At this point, you can optionally connect to the two Memcached backend servers by connecting to ports `11212` and `11213` and running the command `get quickstart-data` on both. One of the two servers returns the value `Hello!`. That server is the one that the proxy chose as the backend to store the data under the `quickstart-data` key.

## Clean up

When you are finished with this tutorial, you can stop the three Memcached processes that you launched in the previous steps.

Because this quickstart didn't have you install the Memcached software that you built, you can clean up by deleting the source directory that you downloaded.

If you want to keep the Memcached software that you built with the proxy features enabled, then you can install it through one of these methods:

* Install Memcached in the default location on your file system, such as `/usr/local/bin`, by running `sudo make install`.

* Manually copy or move the `memcached` binary to the location of your choice.

You also have the option of rebuilding the `memcached` binary with different configuration options. For more information, run the command `./configure --help` from within the Memcached source directory.

