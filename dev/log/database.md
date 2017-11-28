# Database Logger

This logger will insert all log messages to database. It can be useful if you're having multiple web servers behind load
balancer and you quickly want to centralize log messages from all servers in one place. 

## Configuration

To insert log messages to database, you need to define database adapter name and table name.

```php
[
	'enabled' => true,
	'adapter_class' => '\Koldy\Log\Adapter\Db',
	'options' => [
		'log' => ['debug', 'notice', 'info', 'warning', 'error', 'sql', 'critical', 'alert', 'emergency'],
		'adapter' => null,
		'table' => 'log'
	]
]
```


## Table Structure

### MySQL

```sql
CREATE TABLE `log` (
	`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
	`time` timestamp NOT NULL,
	`level` enum('debug','notice','sql','info','warning','error','alert','emergency','critical') NOT NULL,
	`who` varchar(255),
	`message` mediumtext CHARACTER SET utf16,
	PRIMARY KEY (`id`),
	KEY `time` (`time`),
	KEY `level` (`level`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### PostgreSQL

```sql
CREATE TYPE log_level AS ENUM ('debug','notice','sql','info','warning','error','alert','emergency','critical');
CREATE TABLE log (
    id bigserial PRIMARY KEY,
    time TIMESTAMP NOT null,
    level log_level NOT null,
    who varchar(255),
    message text
);
CREATE INDEX i_log_time ON log(time);
CREATE INDEX i_log_level ON log(level);
```



## Options

### Log

This is standard array of all log message levels that you want to forward to this logger.

### Adapter

This is [database adapter name](../database.md#configuration) that will be used for connecting to database.

### Table

This is the name of table where log records will be inserted.

## Additional Options

### File Name Function

`get_insert_fn` should be function that accepts instance of [\Koldy\Log\Message](../log.md#log-elements) and
`options` array. This function should return instance of [Insert](../database/insert-query-builder.md) or `null` if
you don't want this message to be inserted in database. If you create [Insert](../database/insert-query-builder.md)
instance, then you're responsible about deciding which database adapter and table should be used. Framework won't do
anything on the [Insert](../database/insert-query-builder.md) instance, it'll just execute it.

Here's the example of this configuration key:  

```php
'get_insert_fn' => function (\Koldy\Log\Message $message, array $config) {
	
	$insert = new \Koldy\Db\Query\Insert('log');
	$insert->setAdapter('some-connection');
	$insert->add([
		'log_time' => $message->getTime()->format('Y-m-d H:i:s'),
		'level' => $message->getLevel(),
		'text' => $message->getMessage(),
		'who_did_it' => $message->getWho()
	]);
	
	return $insert;
}
```

You may use this when you have different table structure than we [suggested](#table-structure).


[&larr; Back To Log](../log.md#available-log-writers)