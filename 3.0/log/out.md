# Out Logger

This logger will print all log messages with `print()` function. There's no sense of using it in normal HTTP request,
but it makes good sense to use it in CLI scripts. Thanks to this logger, you're able to see all `Log` usage directly
in your terminal.

## Configuration

```php
[
	'enabled' => (PHP_SAPI == 'cli'),
	'adapter_class' => '\Koldy\Log\Adapter\Out',
	'options' => [
		'log' => ['debug', 'notice', 'info', 'warning', 'sql', 'error', 'critical', 'alert', 'emergency'],
		'dump' => ['speed', 'memory']
	]
]
```

## Options

### Log

This is standard array of all log message levels that you want to forward to this logger.

### Dump

This is standard array of what you want to see when [CLI script](../cli.md) is done.


[&larr; Back To Log](../log.md#available-log-writers)