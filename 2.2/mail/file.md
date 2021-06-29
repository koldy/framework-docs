# Sending Email To File

When using this adapter, all emails you're trying to send will create file on file system with all information attached
within an email. This is not useful on production server, but it's useful in development.

## Configuration

```php
return [
	'file' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\File',
		'options' => [
			'location' => 'storage:mail'
		]
	]
];
``` 

[&larr; Back To Mail](../mail.md#available-mail-adapters)