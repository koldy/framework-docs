# DELETE Query Builder

The simplest `DELETE` statement example:

```
$delete = new Delete();
$delete->from('user');
$delete->where('id', 5);
$delete->exec();
```

Or:

```
Db::delete('user')->where('id', 5)->exec();
```

> Beware that if you forget [WHERE statement(s)](where.md), you'll delete entire table.

You can use [WHERE statements](where.md) on `delete` query builder so you can build complex *where* conditions.

To get how many rows was deleted from table, use:

```
$rowsDeleted = $delete->rowCount();
```