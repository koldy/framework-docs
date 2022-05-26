# URL

This is helper class for holding information on URL from which you can get various parts. When constructed, it'll parse your URL or `Koldy\Url\Exception` will be thrown.

You can use this class as method's argument type.

Example:

```
$url = new \Koldy\Url('https://koldy.net/framework/docs/3.0/url?page=1#hash-value');
```

| method | result |
| --- | --- |
| `$url->getScheme()` | `https` |
| `$url->getHost()` | `koldy.net` |
| `$url->getPort()` | Throws exception because port is not set |
| `$url->getUser()` | Throws exception because user is not set |
| `$url->getPass()` | Throws exception because pass is not set |
| `$url->getPath()` | `/framework/docs/3.0/url` |
| `$url->getQuery()` | `page=1` |
| `$url->getFragment()` | `hash-value` |
| `$url->getUrl()` | `https://koldy.net/framework/docs/3.0/url?page=1#hash-value` |
