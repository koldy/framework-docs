# Email Logger

This logger will send an email. It is useful only for `emergency` or `critical` log levels and you have to be careful
when to use it. It is not recommended for high traffic web sites, but it is recommended for small sites when you don't
want to use any external tracking service, but still want to be alerted when something goes wrong.

## Configuration

```php
[
	'enabled' => (PHP_SAPI == 'cli'),
	'adapter_class' => '\Koldy\Log\Adapter\Email',
	'options' => [
		'log' => ['critical', 'alert', 'emergency'],
		'to' => 'address@email.com',
		'adapter' => 'default',
		'send_immediately' => PHP_SAPI == 'cli'
	]
]
```

## Options

### log

This is standard array of all log message levels that you want to forward to this logger.

### to

This is the email address where reports should be sent. If you want to send an email to multiple address, simply define
this as array:

```php
'to' => ['address1@email.com', 'address2@email.com']
```

### adapter

This is the name of adapter from [mail configuration](../mail.md#configuration) which will be used to send an email.

### send_immediately

If `send_immediately` is set to `true`, then email will be sent immediately. If you call `Log::emergency()` multiple times
within one request, then multiple emails will be sent. That's why we recommend this to be `true` only in
[CLI](../cli.md) environment where such setting is useful, and `false` for HTTP requests.

### get_mail_fn

In case you want to customize your email adapter before sending an email, add `get_mail_fn` which must return instance
of `\Koldy\Log\Adapter\AbstractLogAdapter`.

E.g.: this is useful if you're using PHPMailer and you want to put some custom stuff, like:

```php
'get_mail_fn' => function () {
	$mail = \Koldy\Mail::create('phpmailer');
	$mail->getPHPMailer()->SMTPSecure = false;
	return $mail;
}
```


[&larr; Back To Log](../log.md#available-log-writers)
