# File Cache Storage

This cache adapter will store all of your data into files somewhere on the server's filesystem. Every key you store
represents one file on filesystem. If you have a lot of files, then you should sometimes run `Cache::deleteOld()` method.

To reduce wait time on file system and improve performance, storing values to file cache storage won't create or write
to filesystem in the moment you call `Cache::set()`. Instead, it'll be stored in memory and value will be written to
file when request (or) [CLI script](../cli.md) is done with execution.

Beware that framework will not delete files with expired values automatically. You are responsible for cleaning up old
files using `Cache::deleteOld()` method. Old files will be deleted only in one case - if you call `Cache::has()` on
the key that is old enough so framework sees the old file. Otherwise, nothing will happen.


## Configuration

```php
'some-key-name' => [
	'enabled' => true,
	'adapter_class' => '\Koldy\Cache\Adapter\Files',

	'options' => [
		'path' => null,
		'default_duration' => 3600
	]
]
```

### Options

#### Path

If path is defined, then all files will be stored to the path mentioned here. If `null`, all files will be stored to
`log` folder in storage (`storage/log`).

#### Default Duration

Every time when you call `Cache::add()` or `Cache::set()` methods, you can pass your custom duration on how long the
value is valid. If you don't pass duration, `default_duration` will be used. If `default_duration` is not set, then
`3600` is used, which equals one hour.


> Beware that file cache storage is not recommended on systems with high traffic. If you're using SSD storage on your
server, writing and reading from filesystem will be fast, but it'll reduce the life your SSD.


[&larr; Back To Cache](../cache.md#available-cache-engines)