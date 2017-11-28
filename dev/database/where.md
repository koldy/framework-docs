# WHERE Statements

The `WHERE` part is separated because it can be combined with [Select](select-query-builder.md),
[Update](update-query-builder.md) and [Delete](delete-query-builder.md) query
builder instances. `WHERE` can be initialized and because of that, you can build
complex WHERE statements. Here you'll find a lot of examples and various combinations on how to use it.


## Simple statements

The simplest where statement is when you use it on `Select`. Same is for `Update`.

```
$select = new Select('user');
$select->where('id', 5);

$update = new Update('user');
$update->set('first_name', 'John');
$update->where('id', 5);
$update->exec();
```

The two examples above are pretty straight forward. `where()` method is pretty powerful because it accepts various range
of parameter combinations. It's made to feel straight forward when building query. So here are some combinations you can
use:

1. `where(column, value)` - if you pass two parameters, then first parameter is the column name which has to have the
given value - by default, `=` will be used as operator
2. `where(column, '!=', value)` - if you need to put the operator, simply put it between first and third parameter. You
can use other operators like `LIKE` and `ILIKE` - anything that your database accepts
3. `where(instance of Where)` - you can pass instance of `Where` on the first place to make nested WHERE statement
4. `where(Db::expr("anything = 'you want'"))` - you can pass instance of `Expr` in which case nothing will be parsed
or escaped. In this case, you have to handle everything by yourself. This is not recommended so use it only if
there's no other way.

If you call `where()` method twice, it'll be *linked* with `AND`. To *link* it with `OR`, simply use method that starts
with `or`, like `orWhere()`. `or` methods accepts the same parameters as their *opposite* version.

```
$select = new Select('user');
$select->where('id', 5)->orWhere('id', 7);
```

Other WHERE methods usually have their negation version, like `not in` or `not null`. Here they are.


## BETWEEN

```
$select->whereBetween('id', 5, 8);
$select->orWhereBetween('id', 5, 8);
$select->whereNotBetween('id', 5, 8);
$select->orWhereNotBetween('id', 5, 8);
```


## IN

```
$select->whereIn('id', [5, 8, 9]);
$select->orWhereIn('id', [5, 8, 9]);
$select->whereNotIn('id', [5, 8, 9]);
$select->orWhereNotIn('id', [5, 8, 9]);
```


## LIKE

This is alias to `where(column, 'LIKE', value)`. To use PostgreSQL's ILIKE, you'll have to pass that operator manually
using `where()` method.

```
$select->whereLike('first_name', 'John');
$select->orWhereLike('first_name', '%John%');
$select->whereNotLike('first_name', 'John');
$select->orWhereNotLike('first_name', '%John%');
```


## NULL values

This is useful when selecting records with NULL-value columns. `null` methods accepts just column name as parameter.

```
$select->whereNull('age');
$select->orWhereNull('age');
$select->whereNotNull('age');
$select->orWhereNotNull('age');
```


## Nested Where Statements

To nest two or more `Where` statements, simply pass instance of `Where` to another `where()` method. Let's create
SELECT query that selects users where first name is `John` and age is `25` or where last name is `Smith` and age is `30`:

```
$select = new Select('user');

$where1 = Where::init()->where('first_name', 'John')->where('age', 25);
$where2 = Where::init()->where('last_name', 'Smith')->where('age', 30);

$select->where($where1)->orWhere($where2);
```  

The query will look like this:

```
SELECT
	*
FROM
	user
WHERE
	((first_name = 'John') AND (age = 25))
	OR ((last_name = 'Smith') AND (age = 30))
```