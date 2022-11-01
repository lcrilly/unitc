unitc
=====

A curl wrapper for configuring NGINX Unit
-----------------------------------------

Just provide the configuration URI (e.g. `/config/routes`) and **unitc** will
find the control socket to construct the full address in curl syntax.

```USAGE: unitc [HTTP method] [--quiet] URI```

Providing a JSON configuration on stdin will use the PUT method unless a specific
method is provided. Otherwise a GET is used to read the configuration. A virtual
method INSERT can be used to prepend data when the URI specifies an existing array. 
HTTP methods can be specified in lower case.

[jq](https://stedolan.github.io/jq/) is used to prettify the output, if available.
Required if using the INSERT method.

When making changes, the error log is monitored and new log entries are shown.

Examples:

```shell
unitc /config
unitc /control/applications/my_app/restart
echo '{"*:8080": {"pass": "routes"}}' | unitc /config/listeners
unitc /config < unitconf.json
unitc delete /config/routes
```
