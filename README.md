unitc
=====

A curl wrapper for configuring NGINX Unit
-----------------------------------------

Just provide the configuration URI (e.g. `/config/routes`) and **unitc** will
find the control socket to construct the full address in curl syntax.

Providing a JSON configuration on stdin will use the PUT method unless a specific
method is specified. Otherwise a GET is used to read the configuration.

When making changes, the error log is monitored and new log entries are shown.

Examples:

```shell
unitc /config
unitc /control/applications/my_app/restart
echo '{"*:8080": {"pass": "routes"}}' | unitc /config/listeners
unitc /config < unitconf.json
```


