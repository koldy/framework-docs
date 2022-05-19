# UPDATE Query Builder

The simplest `UPDATE` statement example:

```
$update = new Update();
$update->table('user');
$update->set('first_name', 'John');
$update->set('last_name', 'Smith');
$update->set('age', Db::expr('age + 1'));
$update->where('id', 5);
```

Or:

```
Db::update('user')
	->setValues([
		'first_name' => 'John',
		'last_name' => 'Smith'
	])
	->where('id', 5)
	->increment('age')
	->exec();
```

Both examples will execute:

```
UPDATE user
SET
	first_name = 'John',
	last_name = 'Smith',
	age = age + 1
WHERE (id = 5)
```

To get how many rows was updated in table, use:

```
$rowsUpdated = $update->rowCount();
```

You can use [WHERE statements](where.md) on `update` query builder so you can build complex *where* conditions.

## Update With Order By

Imagine the following example: you have CMS where end user can write a question and available answers. It's possible to
rearrange answers order with drag'n'drop. You're using `answers` table to store `answer_id` and `order_index` which is
real position of each answer and `unique` to prevent two different answers of having the same position.

| answer_id | order_index |
| --- | --- |
| 5 | 0 |
| 8 | 1 |
| 7 | 2 |
| 4 | 5 |
| 1 | 4 |
| 6 | 6 |
| 3 | 3 |

If you delete one of the answers, then you need to update `order_index` for all other answers below deleted answers,
meaning you have to decrement its `order_index` for `1`. Let's say you want to delete `answer_id=7` and decrement all
other values:

```
$oldPosition = Db::select('answers')->where('answer_id', 7)->fetchFirstObj()->order_index;

Db::delete('answers')->where('answer_id', 7)->exec();

Db::update('answers')
	->decrement('order_index')
	->where('order_index', '>', $oldPosition)
	->exec();
```

This is good idea of how things should work, but `update` query will fail in this example with error message from
database saying something about constraint error, duplicate value `4`. How?

After you have deleted record with `answerd_id=7`, your table looks like this:

| answer_id | order_index |
| --- | --- |
| 5 | 0 |
| 8 | 1 |
| 4 | 5 |
| 1 | 4 |
| 6 | 6 |
| 3 | 3 |

And now you're trying to update all other `order_index` values by decrementing them for `1`. Database is going to update
first record which finds under `where` condition, and that's every record with `order_index` greater than `2`. In this
case, first record for updating is `answer_id=4` which needs to get new `order_index` with value `4`. Since `order_index`
column is `unique`, it can't be updated because record under `answer_id=1` already have position `4`.

The solution for this is to use `orderBy()` method so database first orders records and then does the update:

```
Db::update('answers')
	->decrement('order_index')
	->where('order_index', '>', $oldPosition)
	->orderBy('order_index', 'asc')
	->exec();
```
