# Database Basics


## Running Raw SQL Queries

To run any database query, use `\Koldy\Db` class and simply execute:

```
Db::query('SELECT * FROM users')->exec();
```

Note that the above statement will be executed on database, but `exec()` won't return anything. To get the results from `SELECT` statement, use:

```
$users = Db::query('SELECT * FROM users')->fetchAll();
```

Or:

```
$users = Db::query('SELECT * FROM users')->fetchAllObj();
```

In first example, `$users` will be array of arrays of all records from `users` table, while in second example, it'll be array of `stdClass` instances.

Koldy framework provides [query builders](query-builders.md) for [select](query-builders.md#select), [insert](query-builders.md#insert), [update](query-builders.md#update) and [delete](query-builders.md#delete) statements so you should avoid writing raw queries, unless there's no other way.


## Named Bindings

To add your own variable values in queries, you should pass them as second parameter using named bindings:

```
Db::query('SELECT * FROM users WHERE id = :id', ['id' => 5])->fetchAll();
```

To prevent SQL injection, you should never do it directly with string concatenation.


## Using Multiple Database Connections

If you take a look on [this configuration example](../database.md#configuration) and you want to execute query on `admin` database, you should do it like this:

```
Db::query('DROP TABLE users')->setAdapterConnection('admin')->exec();
```

Or shorter:

```
Db::getAdapter('admin')->query('DROP TABLE users')->exec();
```


## Database Transactions

To do a database transaction, you should put it in `try/catch` block:

```
try {
	Db::beginTransaction();
	Db::query('DROP TABLE users')->exec();
	Db::commit();
} catch (\Koldy\Db\Exception $e) {
	Db::rollBack();
	throw $e;
}
```

Or if you do it on other database connection:

```
try {
	Db::getAdapter('admin')->beginTransaction();
	Db::getAdapter('admin')->query('DROP TABLE users')->exec();
	Db::getAdapter('admin')->commit();
} catch (\Koldy\Db\Exception $e) {
	Db::getAdapter('admin')->rollBack();
	throw $e;
}
```

### Nesting Database Transactions

When your project grows, there's usually a lot of big procedures that execute a lot of small pieces of code. For example,
let's say that each small piece of code has one try/catch transaction block inside, but you want to run all the pieces
and you want to rollback the database transaction if one of the pieces fail.

Database would deny these queries because it's not possible to run nested SQL transactions. That's why there's implementation
in framework that allows you that: nesting database transactions one into another. Here's how it works:

```
try {
	Db::getAdapter()->beginTransaction();
	
	// some code...
	
	try {
		Db::beginTransaction(); // <- there's already active transaction so this won't be executed

		// some other code...

		Db::commit(); // <- since there's already active transaction, this won't be commited
	} catch (\Koldy\Db\Exception $e) {
		Db::rollBack(); // <- since there's active transaction, this rollBack() won't be executed
		throw $e; // <- this is important because it'll be caught by "parent" transaction block
	}
	
	Db::getAdapter()->commit(); // <- if it's all good, this will be executed
} catch (\Koldy\Db\Exception $e) { // <- if nested try/catch block threw something, it'll be caught here
	Db::getAdapter()->rollBack();
	throw $e;
}
```

In simple cases, one database transaction is usually more than enough, but when you have a huge system, nested transactions are big help.
