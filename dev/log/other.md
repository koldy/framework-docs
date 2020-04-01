# "Other" Logger

If all standard loggers are not useful for your case, then use `Other` logger which does nothing, except it gives you raw
[Log](../log.md#log-elements) messages. This is useful if you need to send log message to some 3rd party tracking
service, like Sentry.

Koldy framework won't provide service specific loggers in its core because every 3rd party service is specific and
it's not possible to create simple enough abstraction without making config too complicated.

So, if you need to send log message(s) over HTTP, use this logger.

## Configuration

```php
[
	'enabled' => true,
	'adapter_class' => '\Koldy\Log\Adapter\Other',
	'options' => [
		'log' => ['debug', 'notice', 'info', 'warning', 'sql', 'error', 'critical', 'alert', 'emergency'],
		'send_immediately' => false,
		'exec' => function ($messages) {
			// do something with messages
		}
	]
]
```

## Options

### log

This is standard array of all log message levels that you want to forward to this logger.

### send_immediately

If `send_immediately` is set to `true`, then `exec` will be immediately executed. If you call `Log::error()` multiple times
within one request, then function defined on `exec` will be executed multiple times. That's why we recommend this to be
`true` only in [CLI](../cli.md) environment where such setting is useful, and `false` for HTTP requests.

### exec

The `exec` part is function which accepts one parameter which can be instance of `\Koldy\Log\Message` or array of
`\Koldy\Log\Message` instances. If you set `send_immediately` to `true`, then only one message will be passed to `exec`
function. If you set it to `false`, then framework will wait script to end and it'll then pass array of all messages to
your `exec` function. It's up to you to decide what works best for your case.

You can put anything you want in your `exec` function because by the time it'll be executed, framework will set up everything.

Example: you want to send your log messages to some external server:

```php
'exec' => function ($messages) {
	$request = new \Koldy\Http\Request();
	$request->setUrl('https://some.other.com/url/logger');
	$request->setMethod(\Koldy\Http\Request::POST);
	
	$body = [];
	foreach ($messages as $message) {
		$time = $message->getTime()->format('Y-m-d H:i:s');
		$who = $message->getWho();
		$level = $message->getLevel();
		$whatHappened = $message->getMessage();
		$body[] = "{$time} [{$level}] {$who}\t{$whatHappened}";
	}
	
	$body = implode("\n", $body);
	
	$request->setParam('log_msg', $body);
	$request->exec();
}
```


[&larr; Back To Log](../log.md#available-log-writers)
