# Validator Rules

This is the list of all possible validator rules with examples.

## alpha

Verifies that value contains characters from the current locale by using [ctype_alpha](http://php.net/manual/en/function.ctype-alpha.php).

```php
$validator = Validator::create([
	'param' => 'alpha'
]);
```

## alphaNum

Works same as [alpha](#alpha), except it allows numbers as well.

```php
$validator = Validator::create([
	'param' => 'alphaNum'
]);
```

## anyOf

Verifies that value is one of the possible values.

```php
$validator = Validator::create([
	'param' => 'anyOf:one,two,3,four'
]);
```

If possible value contains comma, then urlencode the rule:

```php
$validator = Validator::create([
	'param' => 'anyOf:one,two%2C maybe three'
]);
```

## array

Verifies that value is array.

```php
$validator = Validator::create([
	'param' => 'array'
]);
```

If you know how many elements array can have (for example, you want array to have 5 elements), then you can define it like:

```php
$validator = Validator::create([
	'param' => 'array:5'
]);
```

## bool, boolean

Verifies that value is type of `boolean`. Both rules (`bool` and `boolean`) works the same.

```php
$validator = Validator::create([
	'param' => 'bool',
	'param2' => 'boolean'
]);
```

## csrf

This rule is automatically used by framework when CSRF check is enabled in [mandatory configuration](../configuration.md#security),
although you can use it yourself as well if you know how to use it.

```php
$validator = Validator::create([
	'param' => 'csrf'
]);
```

## date

Verifies that value is in the requested format or in some general format recognizable by PHP.

```php
$validator = Validator::create([
	'param' => 'date',
	'param2' => 'date:Y-m-d'
]);
```

In the example above, `param` can be anything that `strtotime()` can recognize. If you define format, then framework
will try to construct date with `DateTime::createFromFormat()` and if it fails, validator will report an error.

Possible formats can be found in
[offical PHP documentation](http://php.net/manual/en/datetime.createfromformat.php#refsect1-datetime.createfromformat-parameters).

## decimal

Verifies that value is numeric with decimal places. Number of decimals must be defined.

```php
$validator = Validator::create([
	'param' => 'decimal:2'
]);
```

## different

Verifies that parameter doesn't have the same value as other parameter in the same data set.

```php
$validator = Validator::create([
	'param1' => null,
	'param2' => 'different:param'
]);
```

## email

Verifies that value is email in the correct format. 

```php
$validator = Validator::create([
	'param' => 'email'
]);
```

## endsWith

Verifies that value ends with the string from parameter:

```php
$validator = Validator::create([
	'phone' => 'endsWith:00', // phone parameter must end with "00"
]);
```

## exists

This validator checks if given value exists in targeted table in database. It'll perform simple
[select query](../database/select-query-builder.md) using [database model](../database/models.md).

For example, let's say you have class `\Db\User` that extends `\Koldy\Db\Model` and it's using databaes table
name `user`. You want to check if there's already a record under `id` column with the given value:

```php
$validator = Validator::create([
	'user_id' => 'exists:\Db\User,id'
]);
```

If your `user_id` is, for example, `5`, framework will search for record in `user` table where `id=5`. If there is
a record present in database, rule will say this is correct, otherwise it'll return an error saying record is missing. 

## hex

Verifies that value contains only hexadecimal characters using [ctype_xdigit](http://php.net/manual/en/function.ctype-xdigit.php).

```php
$validator = Validator::create([
	'param' => 'hex'
]);
```

## integer

Verifies that given value is integer. It allows negative integers as well by ignoring the `-` sign.

```php
$validator = Validator::create([
	'param' => 'integer'
]);
```

## is

Verifies that value has the value requested in rule, literally meaning: *This value is*:

```php
$validator = Validator::create([
	'param' => 'is:yes'
]);
```

This is useful for sign up forms where user must agree to terms and conditions. If checkbox for agreement is named
*agreement* and its check value is *agreed*, you would then validate like:

```php
$validator = Validator::create([
	'agreement' => 'is:agreed'
]);
```

## length

Verifies that the value is string with the precisely this length:

```php
$validator = Validator::create([
	'param' => 'length:20'
]);
```

## max

Verifies that value is numeric with the maximum value of:

```php
$validator = Validator::create([
	'param' => 'max:100'
]);
```

## maxLength

Verifies that the value is string with the maximum length of:

```php
$validator = Validator::create([
	'param' => 'maxLength:20'
]);
```

## min

Verifies that the value is numeric with the minimum value of:

```php
$validator = Validator::create([
	'param' => 'min:1'
]);
```

## minLength

Verifies that value is string with the minimum length of:

```php
$validator = Validator::create([
	'param' => 'minLength:10'
]);
```

## numeric

Verifies that the value is numeric by using PHP's [is_numeric()](http://php.net/manual/en/function.is-numeric.php) function.

```php
$validator = Validator::create([
	'param' => 'numeric'
]);
```

## present

Verifies that this parameter is present in the data set being validated, although parameter is allowed to be empty.

```php
$validator = Validator::create([
	'param' => 'present'
]);
```

## required

Verifies that this parameter is present in data set and not empty.

```php
$validator = Validator::create([
	'param' => 'required'
]);
```

## same

Verifies that the parameter has the same value as other parameter in the same data set.

```php
$validator = Validator::create([
	'password' => 'required',
	'password_confirm' => 'same:password'
]);
```

## slug

Verifies that value is the URL *slug* containing only small characters **a-z**, dashes (`-`) and numbers **0-9**:

```php
$validator = Validator::create([
	'param' => 'slug'
]);
```

You may check the [slug helper](../util.md#slug) for more info.

## startsWith

Verifies that value starts with the string from parameter:

```php
$validator = Validator::create([
	'name' => 'startsWith:John',
	'phone' => 'startsWith:+', // phone parameter must start with "+"
]);
```

You may check the [slug helper](../util.md#slug) for more info.

## unique

This rule will check the database for the presence of value and it'll say it's correct if value is not found in database,
kinda like opposite from [exists rule](#exists). If value is not present in database, it means that value is *unique*.

Let's say you have a sign up form and you want to check if entered email already exists in database.

```php
$validator = Validator::create([
	'user_email' => 'required|email|unique:\Db\User,email'
]);
```

Other example: let's say user is signed into your system and wants to change its email address. You want to validate
if entered email doesn't already exist in your table, but you want to ignore user's records because - user might submit
the form without changing its email address and you probably don't want to say that email is incorrect.

```php
$validator = Validator::create([
	'user_email' => 'required|email|unique:\Db\User,email,5'
]);
```

This example will search the `user` table, but ignoring record(s) where primary key is `5`. You can ignore other column
by defining it:

```php
$validator = Validator::create([
	'user_email' => 'required|email|unique:\Db\User,email,5,id'
]);
```

Now, it's explicitly said that rule should ignore records where `id=5`.

Sometimes, value you want to ignore is present in data set you're validating, so in that case, you can reference it like:

```php
$validator = Validator::create([
	'user_id' => 'required|integer|min:1',
	'user_email' => 'required|email|unique:\Db\User,email,field:user_id,id'
]);
```

[&larr; Back to Validator](../validators.md)