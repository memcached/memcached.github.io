+++
title = 'Install the built-in proxy'
weight = 20
+++

The Memcached built-in proxy is available only if you activate a specific configuration option while compiling Memcached. For this reason, you can run the proxy only if you build Memcached from source. This page guides you through through the following tasks:

* Build and install Memcached with its proxy features enabled.
* Download the Lua library that the proxy requires for common tasks.

For a general overview of the proxy, see [Built-in proxy]({{<proxy_base_path>}}). For a hands-on tutorial that guides you through compiling and running the proxy with a simple configuration, see [Built-in Proxy Quickstart]({{<proxy_base_path>}}quickstart).

## Before you begin {#prereqs}

Make sure that your machine has the necessary development tools and libraries
to build Memcached from source. These requirements are probably available from your operating system's package manager.

For example, on Debian, you can satisfy these requirements by having the following packages installed:

* `gcc`
* `make`
* `libevent-dev`

The steps on this page guide you though launching three Memcached servers on TCP ports `11211`, `11212`, and `11213`. If you already have Memcached services running on those ports and you don't want to disrupt them, then you should take either of these additional steps:

* Substitute different port numbers for this tutorial.
* Run this tutorial as written on a different machine that isn't running Memcached.

## Download and build the proxy {#download}

To download and compile Memcached with its proxy features enabled, follow these steps:

1. Download the latest Memcached release tarball:

    ```console
    wget https://memcached.org/latest
    ```

1. Unzip and untar the tarball, then change your working directory to the source directory:

    ```console
    tar xzvf memcached-{{<var>}}VERSION{{</var>}}.tar.gz

    cd memcached-{{<var>}}VERSION{{</var>}}
    ```
    
    Replace <var>`VERSION`</var> with the version number in the tarball that you downloaded in the previous step.
    
1. Configure the Memcached build process to enable the built-in proxy:

    ```console
    ./configure --enable-proxy {{<var>}}OTHER_OPTIONS{{</var>}}
    ```
    
    Replace <var>OTHER_OPTIONS</var> with a list of any other options for the `configure` command that you wish to enable. For a complete list of these options, run the command `./configure --help`.

1. Build and test Memcached:

    ```console
    make
    
    make test
    ```
    
    If you encounter errors related to missing tools or libraries, make sure that your system has the development packages that you need to build Memcached from source. For more information, see [Before you begin](#prereqs).
    
1. Install the proxy-enabled Memcached on your system.

    ```console
    sudo make install
    ```
    
1. Leave the source directory:

    ```console
    cd ..
    ```
   
1. Confirm that you have installed Memcached with proxy features enabled:

    ```console
    memcached --help | grep proxy
    ```
    
    The output resembles the following:
    
    ```
    - proxy_config:        path to lua library file. separate with ':' for multiple files
    - proxy_arg:           string to pass to lua library
    ```
    
1. Download the route library:

    ```console
    wget https://raw.githubusercontent.com/memcached/memcached-proxylibs/main/lib/routelib/routelib.lua
    ```

1. Move the `routelib.lua` file that you downloaded in the previous step to some permanent place on your file system, and take note of it. You will need to refer to its path when you run the Memcached proxy.
