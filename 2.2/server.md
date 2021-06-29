# Server Info

This helper can help get some useful information about the server where your PHP application is running.


## Server Load

To get the server load, you can use `\Koldy\Server::getServerLoad()`. On *nix systems, it'll return CPU load in standard
format, like `0, 0.01, 0.05`. If server load can't be reached, then `\Koldy\Exception` will be thrown.


## Signature

Server signature will return some common server information in the time when it's called. Calling
`\Koldy\Server::signature()` will return output similar to this:

```
server: 127.0.0.1 (localhost:4001)
CLI Name: test
CLI Script: /vagrant/application/scripts/test.php
Server load: 0, 0.01, 0.05
Memory: currently: 900kb, peak: 942kb, allocated: 2048kb, no limit detected
No. of included files: 15
```


## IP

`\Koldy\Server::ip()` will simply return `$_SERVER['SERVER_ADDR']` or `127.0.0.1`.
