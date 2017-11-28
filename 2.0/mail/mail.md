# Native mail() Function

This adapter will simply use PHP's native [mail()](http://php.net/manual/en/function.mail.php) function. This can work
if you know how to properly configure your server. On most systems, this option won't work or emails will end up in
SPAM folder. 

## Configuration

```php
return [
	'sendmail' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\Mail'
	]
];
``` 

[&larr; Back To Mail](../mail.md#available-mail-adapters)