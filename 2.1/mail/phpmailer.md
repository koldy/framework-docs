# PHPMailer

This adapter is wrapper around popular [PHPMailer](https://github.com/PHPMailer/PHPMailer). Koldy framework doesn't ship
PHPMailer so you have to install it by yourself, otherwise you won't be able to use it.

We recommend this adapter when you want to send email using some external SMTP server.


## Installation

To install PHPMailer, use Composer. There are two ways how to install:

1. Add `"phpmailer/phpmailer": "~6.0"` to `require` section in `composer.json` located in root of your project and then
run `php composer.phar update`
2. Or run `php composer.phar require phpmailer/phpmailer` in the root of your project


## Configuration

This is example of configuration how to send an email over remote SMTP server.

```php
return [
	'phpmailer' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\PHPMailer',
		'options' => [
			'host' => 'mail.example.com',
			'port' => 587,
			'username' => 'someUsername',
			'password' => 'somePassword',
			'type' => 'smtp'
		]
	]
];
```

You can set additional `adjust` property within `options` which is a function that gets PHPMailer instance as first
parameter and this is where you can modify instance properties. PHPMailer has many options and you can use those options
here.


### GMail

Very common case is to use GMail as SMTP provider. Here's configuration example that works for GMail and it's using
`adjust`:

```php
return [
	'phpmailer' => [
		'enabled' => true,
		'adapter_class' => '\Koldy\Mail\Adapter\PHPMailer',
		'options' => [
			'host' => 'smtp.gmail.com',
			'port' => 465,
			'username' => 'your.address@gmail.com',
			'password' => 'yourAppPassword',
			'type' => 'smtp',
			'adjust' => function ($mailer) {
				/** @var $mailer \PHPMailer\PHPMailer\PHPMailer */
				$mailer->SMTPSecure = 'ssl';
			}
		]
	]
];
```

> You can obtain your own app password on [Google Accounts](https://myaccount.google.com/apppasswords).

[&larr; Back To Mail](../mail.md#available-mail-adapters)