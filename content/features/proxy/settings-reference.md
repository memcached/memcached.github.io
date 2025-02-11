+++
title = 'Proxy settings reference'
weight = 110
+++

This page is a reference for the settings you can optionally define to fine-tune the behavior of the Memcached built-in proxy. For a guide to applying these settings, see [Adjust proxy settings]({{<proxy_base_path>}}configure#settings).

## <code>active_req_limit</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>0</code></td></tr>
</table>

The proxy returns a fast fail on any request if it it already actively processing more than this many requests.

If set to `0`, then the proxy does not impose this limit.

## <code>backend_use_tls</code>

<table>
<tr><th>Type</th><td><code>boolean</code></td></tr>
<tr><th>Default</th><td><code>false</code></td></tr>
</table>

Enable TLS by default when connecting to backend servers. Proxy must be
compiled with `--enable-proxy-tls` option.

## <code>backend_connect_timeout</code>

<table>
<tr><th>Type</th><td><code>float</code></td></tr>
<tr><th>Default</th><td><code>5</code></td></tr>
</table>

The time, in fractional seconds, before an attempted connection between the proxy and a backend times out.

## <code>backend_depth_limit</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>0</code></td></tr>
</table>

The maximum number of requests which can be enqueued on a backend before
fast-failing further requests. This limits memory usage and client delays if a
backend is having network problems. This should be set to lower than
`active_req_limit` if used.

If set to `0`, then the proxy does not impose this limit.

## <code>backend_failure_limit</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>3</code></td></tr>
</table>

The number of consecutive connection or validation failures that the proxy tolerates from a backend before the proxy marks the backend as bad.

## <code>backend_flap_backoff_max</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>3600</code></td></tr>
</table>

Maximum number of whole seconds to wait before retrying a flapping server.
Ensures servers with ephemeral issues are occasionally brought back in a
reasonable timeframe.

## <code>backend_flap_backoff_ramp</code>

<table>
<tr><th>Type</th><td><code>float</code></td></tr>
<tr><th>Default</th><td><code>1.5</code></td></tr>
</table>

The proxy uses this fractional second value to determine a backoff for how often
to retry unhealthy backends. The proxy derives the backoff time by multiplying the following numbers:

* The value of <code>backend_flap_backoff_ramp</code>
* The value of <code>backend_retry_waittime</code> 
* The number of times the backend in question has flapped

## <code>backend_flap_time</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>0</code></td></tr>
</table>

The number of seconds a that backend must be held open without errors, or else it
is considered to be flapping.

If set to `0`, then the proxy never marks a backend as flapping.

## <code>backend_read_timeout</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>3</code></td></tr>
</table>

The time, in fractional seconds, that the proxy waits for a response from a connected backend after forwarding a client command to it.

## <code>backend_retry_waittime</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>3</code></td></tr>
</table>

The time, in whole seconds, that the proxy waits before retrying a failed connection with a backend.

## <code>backend_use_iothread</code>

<table>
<tr><th>Type</th><td><code>boolean</code></td></tr>
<tr><th>Default</th><td><code>false</code></td></tr>
</table>

Whether or not a backend is hanndled by worker threads or a dedicated I/O
thread, by default.

The default value of <code>false</code> provides better scalability at the cost of more TCP connections and less batching of backend syscalls.

## <code>buffer_memory_limit</code>

<table>
<tr><th>Type</th><td><code>integer</code></td></tr>
<tr><th>Default</th><td><code>0</code></td></tr>
</table>

The proxy returns a fast fail on any request if more than roughly this many kilobytes are actively in use by requests for request or response value buffers.

If set to `0`, then the proxy does not impose this limit.

## <code>tcp_keepalive</code>

<table>
<tr><th>Type</th><td><code>boolean</code></td></tr>
<tr><th>Default</th><td><code>false</code></td></tr>
</table>

Whether or not all new backends use TCP keepalive.
