# DevNull Cache Storage

All Linux guys know that when you send something to `/dev/null`, it'll just disappear. The same thing is here. Anything
you try to cache with this adapter, nothing will be stored and if you try to use the cached data even within the
same request, cache will return `null` which means that the value you previously stored doesn't exists. Adding and
setting data to this adapter will be always successful.

This adapter is used when other cache storage is disabled or can't be found. If you misconfigure cache configuration
and still try to use it, instead of throwing exceptions, your app will continue to run.


## Configuration

There are no specific options, so configuration example is:

```php
'nowhere' => [
    'enabled' => true,
    'adapter_class' => '\Koldy\Cache\Adapter\DevNull'
]
```

[&larr; Back To Cache](../cache.md#available-cache-engines)