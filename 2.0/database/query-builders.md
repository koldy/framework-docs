# Database Query Builders


## Introduction

Query builders are here to speed up the process of inserting, updating, deleting and selecting one or more rows
from database. We recommend to use query builders for all CRUD operations and you're always encouraged not to write
database queries directly, unless there's no other way.

Query builders we have:

1. [SELECT](select-query-builder.md)
2. [INSERT](insert-query-builder.md)
3. [UPDATE](update-query-builder.md)
4. [DELETE](delete-query-builder.md)

You can use [WHERE statements](where.md) on [SELECT](select-query-builder.md),
[UPDATE](update-query-builder.md) and [DELETE](delete-query-builder.md) query builders.

Framework is using query builders internally for all operations on [database models](models.md).

All query builders are within `\Koldy\Db\Query` namespace, so to use all examples, `use`:

```
use Koldy\Db\Query\{Select, Insert, Update, Delete};
```


## Troubleshooting

One of very useful features is that query builders are formatting SQL queries on human very readable way, so if you
try to `print` instance of any query builder, you'll see nicely formatted query prepared for [PDO](http://php.net/pdo).
If you call `debug()` method on the instance, you'll get formatted query filled with actual values. This is very useful
during development and during troubleshooting.


## Database Connections

If you don't define database connection name on query builder, it'll use default database connection. If you want to
execute query on some other database connection, then you have to explicitly define the name of database connection
by using `setAdapter()` method which accepts database connection name as first parameter.

You don't have to worry about database connection when initiating new query builder from [model](models.md).

