# File Logger

This logger will dump all log messages to file on file system. It'll try to write to file as soon as you call any of
methods in `Log` class.

## Configuration

```php
[
	'enabled' => true,
	'adapter_class' => '\Koldy\Log\Adapter\File',
	'options' => [
		'path' => null,
		'mode' => 0777,
		'log' => ['debug', 'notice', 'info', 'warning', 'error', 'sql', 'critical', 'alert', 'emergency'],
		'dump' => ['speed', 'memory', 'whitespace']
	]
]
```

## Options

### Path

This is path to folder on filesystem where log files should be written. If it's set to `null`, then framework will try
to use `log` folder within your `storage` folder.

### Mode

If log folder doesn't exist, framework will try to create it with the mode you provide here. If it's not set or it's
set to `null`, then `0644` will be used.

### Log

This is standard array of all log message levels that you want to forward to this logger.

### Dump

This is standard array of what you want to see when [CLI script](../cli.md) is done.

### File Name Function

This is optional function which can be used to generate file name dynamically. E.g.:

```php
'file_name_fn' => function () {
	$dt = gmdate('Y-m-d-h');
	return "file-{$dt}.log";
}
```

In this case, you'll get log files per date and per hour. It's easy to combine any other data, such as IP or host name
or anything else you find useful.

[&larr; Back To Log](../log.md#available-log-writers)