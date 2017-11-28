# Data

This is [PHP's trait](http://php.net/manual/en/language.oop5.traits.php) which is meant to provide basic **key-value-storage-like** interface in any class you need it.

When using this trait, you can manipulate with any type of data that you need to *carry* with your class instance. Check the methods below to see what you can use.


## Usage

To use it, simply **include** it in your class:

```
<?php

use Koldy\Data;

class MyClass {

	use Data;

}

```


## Useful Methods

### getData

Gets all data set as array.

### setData

Accepts array as first parameter. This array should be associative array, not numeric.

### set

Sets the value under given key. First parameter is key name, second parameter can be any type.

### has

Returns `true` if key under given name exists. `False` otherwise.

### delete

Deletes the key under given name.

### deleteAll

Deletes all data.

### get

Gets the value under given key name.

### \_\_get and \_\_set

When using this trait, you can access your keys as class properties.