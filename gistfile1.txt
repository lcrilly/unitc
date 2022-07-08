unitc
=====

A curl wrapper for configuring NGINX Unit
-----------------------------------------

Examples:
```unitc /config```

```echo '{"*:8080": {"pass": "routes"}}' | unitc /config/listeners```
