# Cache


## Introduction

When your PHP project gets big, it's most likely that you're start finding bottlenecks in your system. Usual bottleneck
is database, its size and the number of queries. To reduce number of queries, common solution is to store some static
or semi-static query's results to cache, so instead of querying database, you should query your cache engine first.

For example, common data that really doesn't need to be fetched from database every time is the list of world countries,
or the list of currencies and the exchange rate associated to currencies.

Framework offers cache engine abstraction, which means that you shouldn't worry which caching engine your system is
using. All you should worry about is what data could be stored to cache. Later on, you can always switch from one engine
to another just by editing configuration file.

To use all examples from this page, use:

```php
use Koldy\Cache;
```


## Configuration

`/configs/cache.php` is the [pointer config](configs.md#pointer-config), which means that first configuration block 
is default cache engine, while other cache engines can be accessed by providing the key from config file.

It could look like this:

```php
return [
  'files' => [
    'enabled' => true,
    'adapter_class' => '\Koldy\Cache\Adapter\Files',

    'options' => [
      'path' => null,
      'default_duration' => 3600
    ]
  ],

  'nowhere' => [
    'enabled' => true,
    'adapter_class' => '\Koldy\Cache\Adapter\DevNull'
  ],
  
  'other' => [
    'enabled' => true,
    'adapter_class' => '\Koldy\Cache\Adapter\Files',

    'options' => [
      'path' => '/tmp/my-site',
      'default_duration' => 300
    ]
  ]
];
```

Every configuration block has to have three config keys:

1. `enabled` - boolean, is cache enabled or not - if not, [DevNull](cache/dev-null.md) will be used
2. `adapter_class` - this is the class name of the *adapter* that implements `\Koldy\Cache\Adapter\AbstractCacheAdapter`
3. `options` - this is always an array that is passed to constructor of the `adapter_class` when initialising

You can add as many cache engines as you want.

To access the *default* cache engine, use `Cache::getAdapter()`. If you want to get any other cache engine adapter from
configuration, then pass the config key as parameter: `Cache::getAdapter('nowhere')`.

If you make a mistake in cache configuration, framework will throw `\Koldy\Config\Exception`.


## Available Cache Engines

By default, Koldy framework implements all native PHP adapters or adapters that can be made using native PHP functions
and classes. Additional adapters might require additional packages which can be installed using Composer.

Cache engines you can use within this framework are:

1. File storage: [\Koldy\Cache\Adapter\Files](cache/files.md)
2. Memcached: [\Koldy\Cache\Adapter\Memcached](cache/memcached.md)
3. Database: [\Koldy\Cache\Adapter\Db](cache/database.md)
4. No where: [\Koldy\Cache\Adapter\DevNull](cache/dev-null.md)

The `DevNull` engine will be used internally by framework in case when you don't configure any cache engine, but
still decide to use methods from `Cache` class, so instead of throwing exceptions, your project will continue to work,
but nothing will be stored and every attempt of fetching data from cache will result in returning `NULL`.


## Creating Custom Cache Storage Adapter

To create custom cache storage adapter, simply create class in library which extends `\Koldy\Cache\Adapter\AbstractCacheAdapter`,
implement all required methods and then put the class name in cache configuration under [adapter_class](#configuration)
and that's it.


## Working With Cache

To manipulate data in cache, we recommend that you use static methods in `Cache` class. That way, framework will always
use the default cache engine.


### Storing Data To Cache

To store the data to cache, use:

```php
Cache::set($key, $value, $seconds);
Cache::setMulti($array, $seconds);
Cache::setForever($key, $value);
```

To set one value under one key, use `set()` method. `$value` can be anything, including object instances as long as it's
possible to serialize data. Beware that set method will try to write to cache engine immediately, so don't put it in
loops if your cache engine is able to set multiple values at once.

Use `setMulti()` when you want to store multiple values at once. First parameter has to be associative array.

The `$seconds` parameter is optional. Use it only if you know for how long cache engine should keep your data. If the
parameter is not provided, then `default_duration` will be used from [options](#configuration) section. If
`default_duration` doesn't exist, then `3600` will be used, which is equivalent to one hour.

The `setForever()` method will use `set()` method and it'll pass number of seconds that is equivalent to `15 years`.
We think that's long enough to say *it's forever*.


### Getting Data From Cache

To get the data from cache, use:

```php
Cache::has($key);
Cache::get($key);
Cache::getMulti(['one', 'two', 'three']); // might return ['one' => 1, 'two' => 2, 'three' => null]

Cache::getOrSet($key, function () {
	return 'value';
}, $seconds);
```

`has()` method returns boolean if requested key exist in cache or not. Sometimes, to reduce unnecessary calls to cache
engine (for example - file storage), `has()` method will fetch the data and value will be kept in request's scope for
eventual further use.

`get()` method will return value under requested key, or `NULL` if key wasn't found.

`getMulti()` will return values for all requested keys in the given array. `NULL` will be returned for the keys that
weren't found.

`getOrSet()` method will first try to fetch the value under requested key and if it's not found, `Closure` will be
executed and framework will expect return value from the `Closure` function. Returned value will be stored to cache and
it'll be returned back from `getOrSet()` method.


## Using Multiple Cache Adapters

If you have more than one cache adapter configured in `configs/cache.php`, to access other storage adapter simply use:

```php
Cache::getAdapter('name');
```

The `getAdapter()` method will return instance of `\Koldy\Cache\Adapter\AbstractCacheAdapter` which has the same methods
as the root `\Koldy\Cache` class. Here are few examples how to store and read from other cache adapters:

```php
Cache::getAdapter('other')->set($key, $value);
Cache::getAdapter('other')->get($key);
```