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
		'file_mode' => 0660,
		'log' => ['debug', 'notice', 'info', 'warning', 'error', 'sql', 'critical', 'alert', 'emergency'],
		'dump' => ['speed', 'memory', 'whitespace']
	]
]
```

## Options

### path

This is path to folder on filesystem where log files should be written. If it's set to `null`, then framework will try
to use `log` folder within your `storage` folder.

### mode

If log folder doesn't exist, framework will try to create it with the mode you provide here. If it's not set or it's
set to `null`, then `0644` will be used.

### file_mode

If set, it'll change default permissions on created file to the file_mode you have set. Example: `0660` which is equivalent
`rw-rw----`.

### log

This is standard array of all log message levels that you want to forward to this logger.

### dump

This is standard array of what you want to see when [CLI script](../cli.md) is done.

### file_name_fn

This is optional function which can be used to generate file name dynamically. E.g.:

```php
'file_name_fn' => function () {
	$dt = gmdate('Y-m-d-h');
	return "file-{$dt}.log";
}
```

### get_message_fn

This is optional function which can be used to generate custom message in the log. This overrides default message in the log. E.g.:

```php
'get_message_fn' => function (\Koldy\Log\Message $message) {
    $time = $message->getTime()->format('y-m-d H:i:s.v');
    $level = strtoupper($message->getLevel());
    $space = str_repeat(' ', 10 - strlen($level));
    $who = $message->getWho() ?? \Koldy\Log::getWho();
    return "{$time} {$level}{$space}{$who}\t{$message->getMessage()}\n";
}
```

In this case, you'll get log files per date and per hour. It's easy to combine any other data, such as IP or host name
or anything else you find useful.

[&larr; Back To Log](../log.md#available-log-writers)
