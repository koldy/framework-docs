# INSERT Query Builder

The simplest `INSERT` statement example:

```
$insert = new Insert();
$insert->into('user');
$insert->add([
  'first_name' => 'John',
  'last_name' => 'Smith'
]);
$insert->exec();
```

Or:

```
Db::insert('user')
  ->add([
    'first_name' => 'John',
    'last_name' => 'Smith'
  ])
  ->exec();
```

You can `add()` as many records as you want, it'll insert multiple records at once with one query. If you have array
of array values, you can insert all at once with `addRows()`:

```
Db::insert('user')
  ->addRows([
  	['first_name' => 'John','last_name' => 'Smith'],
  	['first_name' => 'Derek','last_name' => 'McSmith']
  ])
  ->exec();
```


## INSERT INTO SELECT

You can copy records from one table to another using INSERT INTO SELECT statements. Simply pass instance of `Select` to
instance of `Insert`:

```
$select = new Select('user');
$select->fields(['first_name', 'last_name']);
$select->whereBetween('id', 10, 20);

$insert = new Insert('user');
$insert->selectFrom($select);
```

This will generate the following query:

```
INSERT INTO user
(
	SELECT first_name, last_name FROM user WHERE (id BETWEEN 10 AND 20)
)
```