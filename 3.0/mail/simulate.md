# Simulating Sending Email

During development, you probably don't want to actually send an email, so instead of really sending email, you can
simulate sending by dumping information about email to log.

## Configuration

```php
return [
	'simulate' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\Simulate'
	]
];
``` 

[&larr; Back To Mail](../mail.md#available-mail-adapters)