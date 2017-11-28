# Database

Koldy framework provides easy database querying either through raw statements or by using its [simple ActiveRecord implementation](database/models.md). You are not encouraged to write SQL statements unless there's no other way.

All database queries can be simply logged if you enable `sql` in [Log config](log.md#configuration). When using framework's ORM, all generated SQL statements will be nicely formatted and logged so you can troubleshoot it easily.

We highly recommend that you enable `sql` file log in development and monitor your log file with [Less](https://en.wikipedia.org/wiki/Less_(Unix)), [Tail](https://en.wikipedia.org/wiki/Tail_(Unix)) or any other program that can show appends to a file. By doing that, you'll see all database queries you call using framework's methods.

## Configuration

Configuration for databases you want to access should be placed in [configs directory](project-structure.md#configs) and it has to be [pointer config style](configs.md#pointer-config).

Configuration example:

```
<?php

return [
	'default' => 'admin',
	'website' => [
		'type' => 'mysql',
		'host' => '127.0.0.1',
		'port' => 3306,
		'username' => 'vagrant',
		'password' => 'vagrant',
		'database' => 'vagrant',
		'persistent' => true,
		'adapter_options' => [
			PDO::MYSQL_ATTR_USE_BUFFERED_QUERY => true
		]
	],
	'admin' => [
		'type' => 'postgres',
		'host' => '127.0.0.1',
		'port' => 5432,
		'username' => 'vagrant',
		'password' => 'vagrant',
		'database' => 'vagrant',
		'persistent' => true,
		'schema' => 'public'
	],
	'file' => [
		'type' => 'sqlite',
		'path' => 'storage:data/mydb.sqlite'
	],
	'file2' => [
		'type' => 'sqlite',
		'path' => '/var/db/mydb.sqlite'
	]
];
```

As you can see in examples above, it is key-value configuration. Keys are database adapter connection name, so in this example, database connection names are `default`, `website`, `admin`, `file` and `file2`.

When you try to use database and you don't provide adapter name, framework will try to use the first key from configuration. Since this is pointer config file and `default` points to `admin`, `admin` adapter connection will be used *by default*.

In the configuration example above, you have example of connecting to [MySQL](#mysql), [PostgreSQL](#postgres) and [Sqlite](#sqlite) databases. All these configuration blocks are similar, but each database has slightly different configuration options which can be found in the following sections.

If you need to get the database [Config](configs.md) instance for some reason, please use:

```
$config = \Koldy\Db::getConfig();
```


### MySQL

To setup connection to MySQL database, use the following config options:

| key | description | value |
| --- | --- | --- |
| type | The connection type, used by framework | required: `mysql` |
| host | Host name of database, can be IP or domain | required |
| port | Database port | optional, `3306` by default |
| username | Database username | required, or leave empty string |
| password | Database username | required, or leave empty string |
| database | Database name on which you're trying to connect | required |
| persistent | Boolean flag weather you want your connections to be persistent | required |
| adapter_options | Array of additional PDO constant options | optional, check [PDO MySQL](http://php.net/pdo-mysql) |


### PostgreSQL

To setup connection to MySQL database, use the following config options:

| key | description | value |
| --- | --- | --- |
| type | The connection type, used by framework | required: `postgres` |
| host | Host name of database, can be IP or domain | required |
| port | Database port | optional, `5432` by default |
| username | Database username | required, or leave empty string |
| password | Database username | required, or leave empty string |
| database | Database name on which you're trying to connect | required |
| persistent | Boolean flag weather you want your connections to be persistent | required |
| schema | The schema you're connecting to | optional |
| adapter_options | Array of additional PDO constant options | optional |


### Sqlite

To setup connection to MySQL database, use the following config options:

| key | description | value |
| --- | --- | --- |
| type | The connection type, used by framework | required: `sqlite` |
| path | Path to database file | required |
| adapter_options | Array of additional PDO constant options | optional |

If path starts with `storage:`, then file path will be relative to the [application's storage directory](project-structure.md#storage).