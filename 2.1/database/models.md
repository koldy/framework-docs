# Database Models

Database models are simplified implementation of ActiveRecord. Every table in database should have its own PHP class which allows you to easily select, insert, update or delete record from that table.

Before continuing with models, make sure you have properly [configured database connections](../database.md#configuration).


## Creating Model Class

To map your PHP class to table in database, simply create class that extends `\Koldy\Db\Model`.

For example, let's say you create file on `library/Db/User.php`:

```
<?php

namespace Db;

use Koldy\Db\Model;

class User extends Model
{
}
```

Your `\Db\User` model is created and ready to use, but since there's nothing else configured in model, framework will do assumptions which might not be correct. For the example above, framework assumes:

1. Table name is the same as class name. If class name is `\Db\User`, then assumed database table name is `db_user`.
2. Database connection is not set, so connection used in this model will be the first connection defined in [configs/database.php](../database.md#configuration).
3. Primary key is not configured, so framework assumes you have integer `id` column which is primary key in your table.
4. Based on assumption `3`, framework assumes that your integer `id` is auto incrementing on each insert.

If these assumptions are good in your case, then there's really nothing else you need to do.


## Configuring

To override framework's assumptions from above, you should define `protected static` properties in your class.


### Database Table Name

```
protected static $table = 'user';
```

If you define `$table`, framework will know that it should use table named `user` and not `db_user`.


### Database Connection Adapter

If you take a look on [database configuration example](../database.md#configuration) and you want your model to connect to `admin` database, then simply add:

```
protected static $adapter = 'admin';
```


### Primary Key

If your primary key column is not named `id`, then define its name like:

```
protected static $primaryKey = 'user_id';
```

If you're using multi-column primary key in your database table, then define it like array:

```
protected static $primaryKey = ['user_id', 'group_id'];
```


### Auto Increment

Framework always think you have auto-increment primary key because this is common behaviour in most cases so you should define this in any other case.

```
protected static $autoIncrement = false;
```

Set `$autoIncrement` to `false` if there's no auto-increment column in your table. If your table has multi-column primary key, you should still set `$autoIncrement` to `false`.


#### PostgreSQL Increment Sequence

When working with PostgreSQL, creating *auto increment* column creates named sequence which is by default formatted like `[tableName]_[pkName]_seq`. This name has to be used when fetching last increment ID. Framework handles this for you, but if you ever encounter on case where you can't use standard sequence name, then sequence name should be defined as:

```
protected static $autoIncrement = 'my_pk_seq';
```

If your sequence is not named by standard and you don't define its name with `$autoIncrement`, then you won't be able to get primary key value when you insert new record using `Model::create()` method.

> Be aware that no error will be raised in this case.


### Model's Configuration Example

This is how your configuration might look like. We'll use this example for next examples. Let's assume this table has `id`, `first_name`, `last_name` and `created_at`. We'll create getters for those as well.

```
<?php

namespace Db;

use Koldy\Db\Model;

class User extends Model
{
    protected static $table = 'user';
    protected static $adapter = 'admin';
    protected static $primaryKey = 'id';
    protected static $autoIncrement = true;

    public function getId(): int
    {
        return (int)$this->id;
    }

    public function getFirstName(): string
    {
        return $this->first_name;
    }

    public function getLastName(): string
    {
        return $this->last_name;
    }

    public function getCreatedAt(): string
    {
        return $this->created_at;
    }
}
```


## Creating / Inserting

To insert data in your table, use `create` static method:

```
$user = User::create([
    'first_name' => 'John',
    'last_name' => 'Smith',
    'created_at' => gmdate('Y-m-d')
]);
```

`$user` is now instance of `\Db\User` class. What framework actually did was:

1. Inserted record in database
2. Fetched auto increment value from database, put it in your model and returned result like this:

```
return new User([
    'id' => 5,
    'first_name' => 'John',
    'last_name' => 'Smith',
    'created_at' => '2017-08-20'
]);
```

As you can see, initializing new instance of the class doesn't do anything. It just takes data and holds it until you call next method. Actual `insert` was performed using static `create` method.

Alternative way of inserting records in database is the other way around: first create model instance and then insert it:

```
$user = new User([
    'first_name' => 'John',
    'last_name' => 'Smith',
    'created_at' => '2017-08-20'
]);

$user->save();
```

Be aware that if your model contains primary key value and you call `save()` method, it'll perform `UPDATE` and not `INSERT`.


## Updating

Now that you inserted model and have its instance, you can change it's properties:

```
$user->first_name = 'Michael';
$user->save();
```

Calling `save()` will perform update if there are fields different than the original data meaning that framework won't touch the database if there is no need to. `Save` can perform `UPDATE` only if model contains primary key value.

In many cases, you'll want to update records in table without making model's instance first. To do that, use static `update` method on `\Db\User`:

```
User::update(['first_name' => 'Derek'], 1);
```

The above example will update `first_name` to `Derek` on the record where primary key is `1`.

If you want to update record by some other criteria, simply pass it as array:

```
User::update(['first_name' => 'Derek'], ['last_name' => 'Smith']);
```

This example would set `first_name` to `Derek` on all records where `last_name` is `Smith`. To pass even more complex WHERE statements, use instance of [WHERE](where.md).


## Deleting

When you have your model's class instance ready, you can use `destroy` method to delete it from server.

```
$user->destroy();
```

This will delete record from database, but `$user` instance will remain with its original data inside.

If you want to delete one or more records from database without making model's instance first, simply use `delete` method on `\Db\User`:

```
User::delete(1);
```

The above example will delete record in `user` table where primary key is `1`.

Same as update, you can pass more complex where statements:

```
User::delete(['first_name' => 'Derek', 'last_name' => 'Smith']);
```

The above example will delete all records where `first_name` is `Derek` and `last_name` is `Smith`. To pass even more complex WHERE statements, use instance of [WHERE](where.md).


## Retrieving Models

Now that you know how to manipulate with records in database, you should see how easy you can retrieve records back from database.


### Retrieve One Record

To fetch one record and get the model back, you can use your primary key:

```
$user = User::fetchOne(1);
```

If there's a record in `user` table with primary key `id=1`, you'll get back instance of `\Db\User` filled with all columns from database. If there's no record in database, you'll get `null` back.

You can pass different conditions:

```
$user = User::fetchOne('first_name', 'John');
// same as:
$user = User::fetchOne(['first_name' => 'John']);
```

Be aware that if you have more than one record where this condition is met, you'll still get one record back and its model instance, and it's gonna be the first record database decides to give you back.

When fetching one record from database, framework grabs the whole table row back using `*` in `SELECT` statement. In same rare cases, you'll want to get only some of the columns. You can define that as third parameter:

```
$user = User::fetchOne('id', 1, ['id', 'first_name', 'last_name']);
```

> Don't forget your primary key column.

Sometimes, you might want to stop your system from execution if there's nothing in database under given parameters. In this case, use:

```
$user = User::fetchOneOrFail(1);
```

In this example, you can be sure that you'll get filled `\Db\User` back. If record wasn't found, framework will throw `\Koldy\Db\Exception\NotFoundException`.

When you have your `$user` model instance, you can access your *columns* directly:

```
echo $user->last_name;
```

We prefer that you create getters for all columns (properties) you tend to use.

If you want to get all data as array, use `getData()` method:

```
print_r($user->getData());
```


### Retrieve Multiple Records

To fetch one or more records at once, you can use simplified `fetch` method, or you can use query builder for more complex cases.

Static method `fetch` accepts few parameters:

1. Where statement, which can be simple as integer, string, array, or instance of [WHERE](where.md)
2. Array of fields you want to get back or all if set `null`
3. Name of the column you want to order by
4. Order direction, `asc` or `desc`
5. How many records you want to fetch
6. From which record you want to start

`5` and `6` are used for `LIMIT`.

```
$users = User::fetch(['last_name' => 'Smith'], null, 'first_name', 'ASC', 100, 50);
```

This example will fetch all records where `last_name` is `Smith`, it'll fetch all columns and array you get back will be sorted by `first_name` ascending and you'll take 100 records, starting from 50th record. What you'll get back is array of `\Db\User` instances.

If there are no records in database, you'll get back empty array.

To perform much complex queries, see [select query builder](query-builders.md) which can be prepared with your model and executed:

```
$users = User::select()
    ->where('last_name', 'Smith')
    ->where('id', '>', 500)
    ->orderBy('last_name', 'ASC')
    ->orderBy('first_name', 'DESC')
    ->limit(0, 100)
    ->fetchAllObj(User::class);
```


### Retrieve One Value

You can easily retrieve value from one column of one row. Let's say there's `birthday` column in `user` table and you want to retrieve birth date of user with `id=5`:

```
$birthday = User::fetchOneValue('birthday', 5);
```

There's `User::fetchOneValueOrFail()` method as well that ensures you get something back, otherwise it'll throw `\Koldy\Db\Exception\NotFoundException`.

But, let's say there's more records you need to query and it all depends on order. For example, let's say you want to retrieve last name of first created `John` in table:

```
$lastNameOfFirstJohn = User::fetchOneValue('last_name', ['first_name' => 'John'], 'created_at', 'asc');
```


### Retrieving Array Of Values

For some reason, let's say you want to get all IDs for all Johns in `user` table:

```
$johnIds = User::fetchArrayOf('id', ['first_name' => 'John'], 'created_at', 'asc', 10);
```

If records with "John" exists, then `$johnIds` will be array of IDs, sorted by `created_at` ascending and limited for up to `10` records. This is useful if you plan to use these IDs in some other query.


### Retrieving Key-Value Pairs

Let's say you have a table with the list of countries in `country` table: `country_name` and its primary key `id`. You want to build `<select>` options so end user can choose one country. To retrieve these key-value options from database, simply use:

```
$keyValuePairs = Country::fetchKeyValue('id', 'country_name', null, 'country_name', 'asc');
```

You'll get all key-value pairs where key is country's `id`, value is country's name, ordered by `country_name` ascending.


### Retrieving All Records From Table

To retrieve all records from table, simply use your model and `all` method. If needed, just pass parameters so database knows how to sort your results.

```
$allUsers = User::all('last_name', 'asc');
```

`$allUsers` will be array of `User` model instances, ordered by `last_name` ascending.


## Transactions

You can use model's class for database transactions as **shorthand**. Imagine that you have a lot of database models and several database connections. Some models connect to one database, some to another. Then, if you want to do a transaction, you need to know which connection you should use. 

Since model has database connection configuration, you can use model without need to think which connection is that.

```
try {
	User::beginTransaction();
	User::create([...]);
	User::create([...]);
	User::commit();
} catch (\Koldy\Db\Exception $e) {
	User::rollBack();
}
```


## Query Builders

You'll encounter on many cases when you want to create custom select query that *starts from a model's table*. If you check the [query builders documentation](query-builders.md), you'll see that when you want to create `select` statement on other-than-default database connection, you would do something like:

```
$select = new \Koldy\Db\Select('user');
$select->setAdapter('admin');
$select->where('id', '<', '10');
$result = $select->fetchAll();
```

To speed up this process, you can use your model's class as **shorthand**:

```
$result = User::select()->where('id', '<', '10')->fetchAll();
```


## Counting Records

You can use your model's class to simply count how many records is present in table.

```
$totalCount = User::count();
```

If you don't pass condition to `count` method, you'll get total number of records in `user` table. Otherwise, you can pass array of conditions or instance of [WHERE](where.md) just like everywhere else to limit count on your condition(s).


## Checking Record Uniqueness

You'll run into case when you want quickly check if some record already exists, like when you have a form for users to sign up and you want to check if given email address already exists.

Let's assume that our example model (`User`) has `email` column which is *unique*. To check if entered email already exists, use:

```
$isDuplicate = User::isUnique('email', 'my@email.com');
```

So, `$isDuplicate` will be `true` if you have a record with `email=my@email.com` or `false` if there's no record with entered email address.

Now, imagine a case when our end user from above successfully created its account, signed in, went to account settings and wants to change its email address. You open up a pre-populated form with the user's email address. When user submits that form, you'll either get the email address that wasn't changed or entirely new address. In both cases, you would probably want to treat this case as success, meaning that you must check if email already exists, but ignoring its own email address.

To do that, pass exception value as third parameter:

```
$isDuplicate = User::isUnique('email', 'new@email.com', 'my@email.com');
```

Fourth parameter of `isUnique` method is exception field name. In case when user wants to update its own email address, you probably have the user's ID remembered in session, meaning that you can check if email is unique by ignoring his own record in table by its `id`. Let's say user's ID is `5`:

```
$isDuplicate = User::isUnique('email', 'new@email.com', 5, 'id');
```

This example will check if there is record in `user` table where `email` is `new@email.com` and where `id` is not `5`.

Method `isUnique` is used in [Validators](validators.md) as well.