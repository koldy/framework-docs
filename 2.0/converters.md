# Converters

This helper has methods that convert one information to another. Here's what it can do.


## bytesToString

It'll convert number of bytes to short, common format:

```
use Koldy\Convert;

echo Convert::bytesToString(2048); // prints "2 M"
```


## stringToBytes

When you get any "bytes shorthand" written like `8M` and you want to convert it to bytes as integer, then use this
method:

```
use Koldy\Convert;

echo Convert::stringToBytes('8M'); // prints 8388608
```


## stringToUtf8

If you don't live in English only area, then you'll probably encounter problem with special characters and possible
solution would be to convert your string to UTF8 string before using it somewhere else (like storing it to database).
In this case, use this method to ensure that the string you want really has UTF8 encoding.

```
use Koldy\Convert;

$utf8string = Convert::stringToUtf8('my string with special characters');
```