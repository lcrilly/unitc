unitc
=====

### A curl wrapper for managing NGINX Unit configuration

```USAGE: unitc [options] URI```

 * **URI** specifies an object of the Unit control API, e.g. `/config` .
 * Configuration data is read from stdin, if present.
 * [jq](https://stedolan.github.io/jq/) is used to prettify JSON output, if
   available.

| Options | |
|---------|-|
| filename … | Read configuration data by concatenating the specified filename(s) instead of reading from stdin.
| _HTTP method_ | It is usually not required to specify a HTTP method. `GET` is used to read the configuration. `PUT` is used when making configuration changes unless a specific method is provided.
| `INSERT` | A _virtual_ HTTP method that prepends data when the URI specifies an existing array. The [jq](https://stedolan.github.io/jq/) tool is required for this option.
| `-q` \| `--quiet` | No output to stdout.

Command line options are case-insensitive and can be specified in any
order. For example, a redundant part of the configuration can be located
by URI and appended with `delete` in a subsequent invocation.

### Local Configuration
For local instances of NGINX Unit, the control socket is automatically
detected. When making changes, the error log is monitored and new log entries
are shown.

| Options | |
|---------|-|
| `-l` \| `--nolog` | Do not monitor the error log after applying config changes.

#### Examples
```shell
unitc /config
unitc /control/applications/my_app/restart
unitc /config < unitconf.json
echo '{"*:8080": {"pass": "routes"}}' | unitc /config/listeners
unitc /config/applications/my_app DELETE
unitc /certificates/bundle cert.pem key.pem
```

### Remote Configuration
For remote instances of NGINX Unit, the control socket on the remote host can
be set with the `$UNIT_CTRL` environment variable. The remote control socket
can be accessed over TCP or SSH, depending on the type of control socket:

 * `ssh://[user@]remote_host[:ssh_port]/path/to/control.socket`
 * `http://remote_host:unit_control_port`

> **Note:** SSH is recommended for remote confguration. Consider the
> [security implications](https://unit.nginx.org/howto/security/#secure-socket-and-state)
> of managing remote configuration over plaintext HTTP.

| Options | |
|---------|-|
| `ssh://…` | Specify the remote Unix control socket on the command line.
| `http://…`*URI* | For remote TCP control sockets, the URI may include the protocol, hostname, and port.

#### Examples
```shell
unitc http://192.168.0.1:8080/status
UNIT_CTRL=http://192.168.0.1:8080 unitc /status

export UNIT_CTRL=ssh://root@unithost/var/run/control.unit.sock
unitc /config/routes
cat catchall_route.json | unitc POST /config/routes
echo '{"match":{"uri":"/wp-admin/*"},"action":{"return":403}}' | unitc INSERT /config/routes
```
