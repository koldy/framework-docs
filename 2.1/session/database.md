# Session Database Storage

To store session information to database, use `\Koldy\Session\Driver\Db` class:

```
return [
	'adapter_class' => '\Koldy\Session\Driver\Db',
	'options' => [
		'connection' => null,
		'table' => 'session
	],

  'cookie_life' => 0,
  'cookie_path' => '/',
  'cookie_domain' => '',
  'cookie_secure' => false,
  'session_name' => 'koldy',
  'http_only' => true
];
```

The `connection` is the name of database connection defined in [database config](../database.md#configuration). If it's
set to null, then default database connection will be used.

The `table` is the table name in database in which session data will be stored.

By default, you won't see any session related query in log. This is default behaviour where idea is not to pollute your
log with session queries. If for any reason you really want to see these queries, add `'log' => true` to `options`
block:

```php
'options' => [
	'connection' => null,
	'table' => 'session,
	'log' => true
]
```

Since all queries are emitted as `SQL` log message, you'll see these queries if `sql` messages are permitted to be logged.
Check [log documentation](../log.md) for more info.

## Table Structure

### MySQL

```mysql
CREATE TABLE `session` (
  `id` varchar(40) NOT NULL,
  `time` int(10) unsigned NOT NULL,
  `data` text CHARACTER SET utf16 NOT NULL,
  PRIMARY KEY (`id`),
  KEY `last_activity_for_gc` (`time`)
) ENGINE=InnoDB;
```

### PostgreSQL

```postgresql
CREATE TABLE session
(
	id varchar(128) not null primary key,
	time integer not null,
	data bytea not null
);

CREATE INDEX last_activity_for_gc ON session (time);
```

[&larr; Back To Session](../session.md#session-storage-configuration)