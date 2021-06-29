# Runtime Cache Storage

This cache adapter will allow you to store your data in script's (or request's) runtime. It'll occupy memory of your PHP script so you have
to take care about memory consumption. It is recommended to store small amount of data and it's recommended to be used in CLI scripts.


## Configuration

There are no specific options, so configuration example is:

```php
'nowhere' => [
    'enabled' => true,
    'adapter_class' => '\Koldy\Cache\Adapter\Runtime'
]
```

[&larr; Back To Cache](../cache.md#available-cache-engines)
