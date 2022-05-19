# SELECT Query Builder

## Introduction

*Select* is most used operation and most complex query builder because of all its capabilities. If you've ever needed to
use `select` queries that are more complex from just using one table in database, then you'll know that most used
parts of `select` query are:

1. [fields](#defining-fields) you want to select, either from one or more tables, using aliases or aggregation functions
2. [from](#defining-from-and-table-aliases) [table name]
3. [joins](#using-joins), next to *from*
4. [where](#where-statements)
5. [group by](#using-group-by)
6. [having](#using-having)
7. [order by](#using-order-by)
8. [limit](#using-limit)

We'll cover it all in next sections. Check the simplest `SELECT` statement example:

```
$select = new Select();
$select->from('user');
```

Or:

```
$select = Db::select('user');
```

Or if you have [`User` model](models.md):

```
$select = User::select();
```

All three examples from above will prepare the following query:

```
SELECT * FROM user
```

Difference between `select` and all other query builders is how you need to run them. When you want to run [insert](#insert),
[update](#update) or [delete](#delete) query statement, you'll probably just call `exec()` method and you're good. If you call
`exec()` method on `select`, query will be executed on database, but you won't get anything back like you would expect to.
To execute `select` query and get something back, you have few methods to choose from.


## Retrieving Records

### Retrieving All Records

```
Db::select('user')->fetchAll();
Db::select('user')->fetchAllObj();
Db::select('user')->fetchAllObj(User::class);
```

Explanations:

1. `fetchAll` will return array of results - it's using [PDO::FETCH_ASSOC](http://php.net/manual/en/pdo.constants.php#pdo.constants.fetch-assoc)
2. `fetchAllObj` will return array of `stdClass` instances - it's using [PDO::FETCH_OBJ](http://php.net/manual/en/pdo.constants.php#pdo.constants.fetch-obj)
3. If you pass class name to `fetchAllObj` method, then framework will fetch results as array and it'll try to create class
instance of every record from result set by passing array of values to class's constructor. Here, if you pass `User::class`,
framework will return you this:

```
$result = [];
foreach (Db::select('user')->fetchAll() as $record) {
	$result[] = new User($record);
}
``` 

This is useful and wanted behaviour when you're working with [database models](models.md), but it'll work with any
class that accepts array of data in constructor.


### Retrieving First Record

```
Db::select('user')->fetchFirst();
Db::select('user')->fetchFirstObj();
Db::select('user')->fetchFirstObj(User::class);
```

These methods are working the same way as methods for fetching all records, except that it resets SQL `limit` and sets the limit
to fetch first record only, so you'll get back `array`, `stdClass` or instance of `User`. If there's nothing fetched from
database, you'll get `null` back.


## Defining Fields

You'll often want to define which columns you want to fetch from table. Use `field` method for that:

```
Db::select('user')
	->field('id')
	->field('first_name')
	->field('last_name', 'surname');
```

The query above looks like this:

```
SELECT
	id,
	first_name,
	last_name as surname
FROM
	user
```

If you want to use aggregation functions, simply put it like this:

```
Db::select('user')
	->field('gender')
	->field('COUNT(*)', 'count_per_gender')
	->groupBy('gender');
```

And the query is:

```
SELECT
	gender,
	COUNT(*) as count_per_gender
FROM
	user
GROUP BY gender
```

## Defining *FROM* And Table Aliases

You have already seen simple `from` statement:

```
$select = new Select();
$select->from('user');
```

As you already know, it's possible to have more than one table *from* where you're selecting your records. Let's say that
each user belongs to `user_group` and each record in `user` table has column `group_id` which is foreign key to `user_group.id`:

```
$select = new Select();
$select->from('user');
$select->from('user_group');
$select->where('user.group_id', Db::expr('user_group.id')); // using Expr because value on right side is not primitive value
```

This example will generate:

```
SELECT
	*
FROM
	user,
	user_group
WHERE
	(user.group_id = user_group.id)
```

If you want to select only columns from `user` table? One way would be to add:

```
$select->field('user.*');
```

Or alternative, you can do it when calling 1st `from()` method:

```
$select->from('user', null, '*');
```

If you want to select specific columns from 1st table and specific columns from 2nd table:

```
$select = new Select();
$select->from('user', null, ['first_name', 'last_name']);
$select->from('user_group', null, ['name']);
$select->where('user.group_id', Db::expr('user_group.id'));
```

You'll get:

```
SELECT
	user.first_name,
	user.last_name,
	user_group.name
FROM
	user,
	user_group
WHERE
	(user.group_id = user_group.id)
```

### Using Aliases In *FROM*

We're recommend using SQL table aliases:

```
$select = new Select();
$select->from('user', 'u', ['first_name', 'last_name']);
$select->from('user_group', 'ug', ['name']);
$select->where('u.group_id', Db::expr('ug.id'));
```

Produces:

```
SELECT
	u.first_name,
	u.last_name,
	ug.name
FROM
	user as u,
	user_group as ug
WHERE
	(u.group_id = ug.id)
```


## Using Joins

There are four *types* of join that framework supports:

1. inner join or just **join** -> methods `innerJoin()` and `join()`
2. left join -> method `leftJoin()`
3. right join -> method `rightJoin()`
4. full join -> method `fullJoin()`

Framework supports building simple and some of the complex join statements. All join methods from above accepts the same
parameters, so to simplify this documentation, all our examples will be based on `INNER JOIN`.


### Simple INNER JOIN Statement

The simplest `INNER JOIN` statement whould be:

```
$select = new Select('user');
$select->innerJoin('user_group', 'user_group.id', '=', 'user.group_id');
```

You'll get:

```
SELECT
	*
FROM
	user
	INNER JOIN user_group ON user_group.id = user.group_id
```


### Using Table Aliases In Join Statements

```
$select = new Select('user', 'u');
$select->innerJoin('user_group ug', 'ug.id', '=', 'u.group_id');
```

You'll get:

```
SELECT
	*
FROM
	user as u
	INNER JOIN user_group ug ON ug.id = u.group_id
```

### Joining More Than One Table

To join more than one table, simply call any join method more than one time:

```
$select = new Select('user', 'u');
$select->innerJoin('user_group ug', 'ug.id', '=', 'u.group_id');
$select->innerJoin('user_email ue', 'ue.user_id', '=', 'u.id');
```

You'll get:

```
SELECT
	*
FROM
	user as u
	INNER JOIN user_group ug ON ug.id = u.group_id
	INNER JOIN user_email ue ON ue.user_id = u.id
```

### Table Joins With More Than One Relation

Sometimes, you'd want join two tables by using more relations. To do that, you can pass array of join statements:

```
$select = new Select('user', 'u');
$select->innerJoin('user_group ug', [
	['ug.id', 'u.group_id'],
	['ug.first_name', 'u.name'],
]);

// to use different operator, simply put it in between, like:
// ['ug.first_name', '!=', 'u.name'],

```

You'll get:

```
SELECT
	*
FROM
	user as u
	INNER JOIN user_group ug ON ug.id = u.group_id AND ug.first_name = u.name
```

All joins from array are joined by `AND`. If you want to put `OR`, simply put it on first position, but then you have to
define `=` for operator:

```
$select = new Select('user', 'u');
$select->innerJoin('user_group ug', [
	['ug.id', 'u.group_id'],
	['OR', 'ug.first_name', '=', 'u.name'],
]);
```

You'll get:

```
SELECT
	*
FROM
	user as u
	INNER JOIN user_group ug ON ug.id = u.group_id OR ug.first_name = u.name
```

Note that join methods always assume that you'll pass another table's column name for the value on right.


## WHERE statements

Check the detailed [documentation here](where.md).


## Using GROUP BY

To create aggregation query, you'll need to use GROUP BY statements every. Method `groupBy()` on `Select` instance accepts
just one parameter. You can pass here anything you want that's meaningful for your database. Here are few examples:

```
$select->groupBy('group_id');
$select->groupBy('u.group_id');
$select->groupBy('1');
$select->groupBy('COUNT(*)');
```

You can add as many GROUP BY statements as you want.


## Using HAVING

More complex queries might have HAVING statements. To define HAVING statement on instance of `Select`, use
`having()` or `orHaving()` methods. If you call `having()` method multiple times, it'll be linked by `AND`, so use
`orHaving()` method to link it with `OR`. Here are few examples:

```
$select->having('group_id', '>', 10);
$select->having('u.group_id', '=', 10);
```


## Using ORDER BY

To sort you results while fetching data, use `orderBy()` method. You can call `orderBy()` method multiple times to sort
your results using multiple columns. Here are few examples:

```
$select->orderBy('first_name'); // will sort it asceding
$select->orderBy('last_name', 'asc');
$select->orderBy('u.age', 'desc');
```


## Using LIMIT

To limit the number of results from `Select`, use `limit()` method. First parameter is the record from which you want to
start fetching results. To fetch all records starting from first result, put `0` as first parameter. 2nd parameter is number
on how many records you want to fetch. So if you pass `limit(0, 10)`, you'll get first 10 records.

```
$select->limit(0, 10);
```

Calling this method multiple times will override any previously set limit settings from before.

In addition to `limit()`, you can you use alternate `page()` method. First parameter in `page()` method is page number, second
parameter is the number of records per page:

```
$select->page(2, 10);
```

Example from above is the same as `$select->limit(10, 10)` - the 2nd page from "10 results per page" result sets.
