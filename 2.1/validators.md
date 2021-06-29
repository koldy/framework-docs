# Validators

## Introduction

Validator class is made for quick data validation. Period.

It's primary function is to validate data on incoming request although it can validate any given key-value array. If
data is not valid, it'll throw `\Koldy\Validator\Exception`.

When creating validator, you should:

1. Provide key-value array where *key* is the parameter name and *value* is the rule
2. Optionally provide key-value data set where *key* is the same *key* as in the first array, and *value* is value you
want to validate.

Let's say you have a form where user needs to enter first name which is mandatory, last name which is not mandatory and
age between 18 and 99. This is how you do it:

```php
use Koldy\Validator;

$validator = Validator::only([
	'first_name' => 'required|minLength:2|maxLength:40',
	'last_name' => 'present|minLength:2|maxLength:40',
	'age' => 'required|integer|min:18|max:99'
]);
```

What this actually does is:

1. Validator is constructed with `only()` method which means you're validating data according to the rules you passed
as first parameter
2. Second parameter is not defined which means that data is taken from HTTP parameters - if it's POST,
then data is taken from `$_POST`, if it's GET, data is taken from `$_GET` parameters and etc.
3. `only()` will require that request contains exactly three parameters and exactly those defined - even if requested
data is valid, but someone sent 4th parameter, Validator will throw an exception. If you want to validate only those
three parameters and allow sending other parameters, then use `create()` instead of `only()`
4. Every parameter has it's own set of rules which can be chained using `|`

If you want to manually validate some data set, simply provide data as second parameter:

```php
$validator = Validator::create([
	'first_name' => 'required|minLength:2|maxLength:40',
	'last_name' => 'present|minLength:2|maxLength:40',
	'age' => 'required|integer|min:18|max:99'
], [
	'first_name' => 'John',
	'last_name' => 'Doe',
	'age' => 21
]);
```

If valid data is sent, you can grab values from validator by getting key-value array or `stdClass`:

```php
$array = $validator->getData(); // $array['first_name'],...
$values = $validator->getDataObj(); // returns stdClass, $values->first_name,...
```

If invalid data is sent, you can catch `\Koldy\Validator\Exception` which contains Validator instance in which error
messages for each parameters are:

```php
try {
	$validator = Validator::only([
		'first_name' => 'required|minLength:2|maxLength:40',
		'last_name' => 'present|minLength:2|maxLength:40',
		'age' => 'required|integer|min:18|max:99'
	]);
} catch (\Koldy\Validator\Exception $e) {
	print_r($e->getValidator()->getMessages());
}
```

Method `getMessages()` will return key-value array where key is the parameter name like `first_name` and value is string
which describes what is the problem. According to example, if you passed only one character for the `first_name`, error
message will be `Has to be at least 2 characters long`.

Beware that as soon as you use static `create()` or `only()` methods, `Validator` will validate data and it'll throw an
exception if data is not valid. If this is not desired behaviour, you can stop validator from automatically
validating and throwing exception by passing `false` as third parameter of `create()` or `only()` methods:

```php
$validator = Validator::only([
	'first_name' => 'required|minLength:2|maxLength:40',
	'last_name' => 'present|minLength:2|maxLength:40',
	'age' => 'required|integer|min:18|max:99'
], false);

if ($validator->isAllValid()) {
	echo 'Good!';
	print_r($validator->getData());
} else {
	print_r($validator->getMessages());
}
```

When you chain more than one rule per parameter, please note that framework will validate data in exact order as defined
in rule. This is important if you're using `exists` or `unique` validators which go to database for their information.
Don't go to database if value is empty and you need it to be required.

Beware that it's usually not enough to put one rule for parameter. For example, if you put:

```php
$validator = Validator::only([
	'age' => 'min:18|max:99'
]);
```

It'll mean: if there's parameter `age` present in data set and `age` is not empty, `Validator` will then check if value
is between 18-99. Failure will happen only if `age` is present, not empty and not between 18-99. To forbid such
cases and to make them strict, use additional rules:

```php
$validator = Validator::only([
	'age' => 'required|integer|min:18|max:99'
]);
```

This example will:
1. check if `age` is present in data set and not empty - if it's not, stop and fail
2. check if value is integer - if it's not, stop and fail
3. check if value is greater than 18 - if it's not, stop and fail
4. check if value is lower than 99 - if it's not, stop and fail


## Available Validator Rules

This framework is shipped with set of very useful validation rules you can find on its own page:
[Validator Rules](validator/rules.md).


## Customizing Error Messages

Framework has its own validator error messages built in which are written to be as much generic as possible so if you
have your system on English, then we're sure it'll be good enough. If it's not good enough, or you want to translate
error messages to other language, feel free to customize it.

All messages are stored in `\Koldy\Validator\Message` class. Let's say you want to translate message *This field is
required* to Croatian, but including the parameter name in the message:

```php
use Koldy\Validator\Message;
Message::setMessageString(Message::REQUIRED, 'Polje {param} je obavezno');
```

To test if it's good:

```php
echo Message::getMessage(Message::REQUIRED, [
	'param' => 'age'
]);
```

And you'll get: *Polje age je obavezno*.


## Customizing Error Messages Dynamically

In the section above, you can see how to set error message by providing string with same basic value replacements. To
completely have different error message regarding on case, you'll have to pass `Closure`:

```php
use Koldy\Validator\Message;
Message::setMessageFunction(Message::REQUIRED, function (array $data) {
	if ($data['param'] == 'age') {
		return 'Age is really required field for us';
	} else {
		return "Field {$data['param']} is required";
	}
});
```

Custom function always must return string.


## Making Your Own Validator Rule

To make your own validator on top of existing validators, simply extend `\Koldy\Validator` class and define your own
validator. Let's say you created `\App\Validator` that validates quantity which will accept only integers between
10-20 and 45-150.

```php
namespace App;

class Validator extends \Koldy\Validator {

	protected function validateQuantity(string $parameter, array $args = []): ?string
	{
		$value = $this->getValue($parameter); // get the actual value from data set
		
		if ($value === null) {
			// act as other rules when we run on NULL
			return null;
		}
		
		$value = (int)$value;
		
		if (!(10 <= $value && $value <= 20) && !(45 <= $value && $value <= 150)) {
			return 'Quantity is not between 10-20 nor 45-150';
		}
		
		return null;
	}

}
```

And then you can use it like:

```php
$validator = \App\Validator::only([
	'product_quantity' => 'required|integer|quantity'
]);
```

As you can see, rules for valid values should always return `null`, and when value is incorrect, rule should return
string saying what's wrong.