# Memcached Cache Storage

This cache adapter will store all of your data to [Memcached](https://memcached.org). It's using PHP's native
[Memcached extension](http://php.net/manual/en/book.memcached.php) so nothing else has to be installed or configured
in your project to use this cache storage.

If you're getting error that `Memcached` class does not exists, then make sure you have properly installed PHP extension
for Memcached. 


## Configuration

```php
'memcached' => [
	'enabled' => true,
	'adapter_class' => '\Koldy\Cache\Adapter\Memcached',

	'options' => [
		'servers' => [ // used in addServers method: http://php.net/manual/en/memcached.addservers.php
			['localhost', 11211]
		],
		'persistent_id' => 'koldy',
		'adapter_options' => []
	]
]
```

## Options

### Servers

This is the exact same array that you would pass to [addServer()](http://php.net/manual/en/memcached.addservers.php)
method. Read more [here](http://php.net/manual/en/memcached.addservers.php).

### Persistent ID

This value is passed to `Memcached` constructor. You can find out more about it [here](http://php.net/manual/en/memcached.construct.php).

### Adapter Options

This array will be passed to `Memcached` [setOptions()](http://php.net/manual/en/memcached.setoptions.php) method. Read
more [here](http://php.net/manual/en/memcached.setoptions.php).

The only option that framework will put here by default is `Memcached::OPT_LIBKETAMA_COMPATIBLE => true` because it'll
increase compatibility with other systems that may access the same Memcached server. Read more about this
[here](http://php.net/manual/en/memcached.constants.php#memcached.constants.opt-libketama-compatible).

### Prefix

This is not mandatory option, but if it's set, then all keys will be prefixed with this.


[&larr; Back To Cache](../cache.md#available-cache-engines)