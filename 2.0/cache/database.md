# Database Cache Storage

This cache adapter will store all of your data to database. All you have to say in configuration is which database
adapter should be used and which table.


## Configuration

```php
'db' => [
  'enabled' => true,
  'adapter_class' => '\Koldy\Cache\Adapter\Db',

  'options' => [
    'adapter' => null,
    'table' => 'cache',
    'default_duration' => 3600,
    'clean_old' => (rand(1, 100) % 100 == 0) // the 1:100 probability to clean old items
  ]
]
```


## Table Structure

Here's the SQL CREATE TABLE statement for the cache table.


### MySQL

```sql
CREATE TABLE `cache` (
	`id` varchar(40) CHARACTER SET latin1 NOT NULL,
	`created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
	`expires_at` int(10) unsigned NOT NULL,
	`data` text NOT NULL,
	PRIMARY KEY (`id`),
	KEY `expires_at` (`expires_at`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### PostgreSQL

```sql
CREATE TABLE cache (
	id varchar(40) not null,
	created_at timestamp not null,
	expires_at int not null,
	data text,
	PRIMARY KEY(id)
);
CREATE INDEX i_cache_expires_at ON cache (expires_at);
```



## Options

### Connection

This is the name of database adapter in [configs/database.php](../database.md#configuration) which will be used for
connection to database. If not set or it's set to `null`, it'll take the default database adapter.

### Table

This is table name where records will be stored. If not set or it's set to `null`, then `cache` table will be used.

### Default Duration

Every time when you call `Cache::add()` or `Cache::set()` methods, you can pass your custom duration on how long the
value is valid. If you don't pass duration, `default_duration` will be used. If `default_duration` is not set, then
`3600` is used, which equals one hour.

### Clean Old

Set this to `true` if you want to delete old records every time when this cache adapter is used. If you put the `rand()`
method to configuration same as the example above, then you'll define probability on when it should be deleted.


[&larr; Back To Cache](../cache.md#available-cache-engines)