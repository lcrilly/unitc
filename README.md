unitc
=====

## A curl wrapper for configuring NGINX Unit

```USAGE: unitc [--quiet] [ssh://…] [HTTP method] URI```

Providing a JSON configuration on stdin will use the PUT method unless a specific
method is provided. Otherwise a GET is used to read the configuration. A virtual
method INSERT can be used to prepend data when the URI specifies an existing array. 
HTTP methods can be specified in lower case.

[jq](https://stedolan.github.io/jq/) is used to prettify the output, if available.
Required if using the INSERT method.

Command line options can be specified in any order. For example, a redundant part
of the configuration can be located by URI and appended with `delete` in a subsequent
invocation.
```shell
unitc /config/routes/0
unitc /config/routes/0 delete
```

### Local configuration

Just provide the configuration URI (e.g. `/config/routes`) and **unitc** will
find the control socket to construct the full address in curl syntax.

When making changes, the error log is monitored and new log entries are shown.

Examples:

```shell
unitc /config
unitc /control/applications/my_app/restart
echo '{"*:8080": {"pass": "routes"}}' | unitc /config/listeners
unitc /config < unitconf.json
unitc delete /config/applications/wp
```

### Remote configuration

The configuration of a remote Unit instance can be controlled by specifying the
control socket as ssh://… or with a URI complete with protocol (http://…).

Using ssh:// is recommended for remote connections to avoid insecure
communications over shared networks. This can be used when the control socket
is listening on a TCP port or Unix socket using ssh(1)/scp(1) syntax:

* `ssh://[user@]remote_host:unit_control_port`
* `ssh://[user@]remote_host/path/to/control.socket`

Alternatively, the remote control socket can be set with the
`$UNIT_CTRL` environment variable.

Examples:
```shell
unitc http://192.168.0.1:8080/config
UNIT_CTRL=http://192.168.0.1:8080 unitc /config

export UNIT_CTRL=ssh://root@unithost/var/run/control.unit.sock
echo '{"match": {"uri":"/foo"}, "action": {"pass":"applications/foo"}}'| unitc /config/routes insert
```
