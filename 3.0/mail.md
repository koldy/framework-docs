# Mail

Koldy framework provides basic functionality for emails for development and production.


## Configuration

To configure all email adapters, use `configs/mail.php` which is [poiter config](configs.md#pointer-config).

```php
return [
	'default' => 'simulate',
	
	'simulate' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\Simulate'
	],
	
	'file' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\File',
		'options' => [
			'location' => 'storage:mail'
		]
	],
	
	'sendmail' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\Mail'
	]
];
``` 


## Available Mail Adapters

Here are currently available mail adapters:

1. [Simulate](mail/simulate.md) - sends email content to log, useful for development only
2. [File](mail/file.md) - sends content of email to file, useful for development only
3. [native Mail](mail/mail.md) - uses PHP's native [mail() function](http://php.net/manual/en/function.mail.php)

If you need PHPMailer support, check [koldy/phpmailer](https://github.com/koldy/phpmailer) package.

## Sending Emails

```php
use Koldy\Mail;

$mail = Mail::create()
	->from('you@example.com')
	->to('someone@example.com')
	->subject('Test email')
	->body('Email\'s test body');

$mail->send();
```

This is the simplest way how to send an email. Failure will produce `\Koldy\Mail\Exception`. To add more information
to the email you're sending, use:

```php
$mail
	->from('you@example.com', 'Your Name')
	->to('someone@example.com', 'Recipient Name');
```

### HTML Emails


To send an HTML email, pass `true` as the second parameter of `body()` method:

```php
$mail->body('<html>...</html>', true);
```

If you want to send both HTML version and plain text version, then pass third parameter containing plain text:

```php
$mail->body('<html>...</html>', true, 'Alternative plain text');
```

### Custom Headers

Sometimes, you may wish to set additional headers. You can do it with:

```php
$mail->setHeader('headerName', 'headerValue');
```

### Attachments

To send an attachment within email, use: 
```php
$mail->attachFile('/var/tmp/file.txt', 'FileName.txt');
```


### Additional Options

There are few additional options you may use:

```php
$mail->replyTo('other@email.com', 'Optional name');

$mail->cc('boss@example.com', 'Boss Name');

$mail->bcc('other.boss@example.com', 'Other Boss Name');
```


## Creating Custom Adapter

To create your own custom custom implementation, simply create class that extends `\Koldy\Mail\Adapter\AbstractMailAdapter`
and implement all required methods. Then you'll be able to set your own class as `adapter_class` in
`configs/mail.php`.
