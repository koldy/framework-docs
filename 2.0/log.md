# Log

## Introduction

Logging is common concern for most developers. There are plenty of ways how to log and there are plenty of PHP libraries
online which can be used without problem. This framework offers some basic log *writers* that doesn't require any other
library. If you need to have something more advanced, you can make your own logger or you can simply use anything else.
It's also possible to combine our log architecture with some 3rd party logger just by *wrapping* it.

To use all examples from this page, use:

```php
use Koldy\Log;
```


## The Idea

The idea is that when you're developing your app, you don't worry about logging. All you have to worry about is
**what** and **when** to log.

Framework offers several *levels* of log messages: `emergency`, `alert`, `critical`, `error`, `warning`, `info`, `notice`,
`sql` and `debug`. So, if you want to log some **info** message, do:

```php
Log::info('Your message');
```

Where this message will end up depends about configuration.


## Configuration

Log configuration is defined within [mandatory configuration block](configuration.md#log). The `log` config in main
configuration is array of log **writers**. Every item of that array is array of three required keys:

| key | type | meaning |
| --- | --- | --- |
| `enabled` | `boolean` | `true` if this *writer* should be used or `false` if this *writer* should be ignored. Since config is PHP file, you can use expression like `PHP_SAPI == 'cli'` which will be `true` only in `CLI` env |
| `adapter_class` | `string` | Class name that should handle the log *calls*. This must be instance of `\Koldy\Log\Adapter\AbstractLogAdapter` |
| `options` | `array` | Array of options passed to the `adapter_class` constructor as first parameter |

Most common logger is **log to file**. Let's make the simplest configuration for this:

```php
'log' => [
	[
		'enabled' => true,
		'adapter_class' => '\Koldy\Log\Adapter\File',
		'options' => [
			'log' => ['debug', 'notice', 'info', 'warning', 'error', 'sql', 'critical', 'alert', 'emergency']
		]
	]
]
```

The example above says:
1. There is one active log *writer*
2. We should take messages from all log levels
3. When we take the message, we'll pass it to [\Koldy\Log\Adapter\File](log/file.md) to let that class decide what to
do with message

Find more options for file *writer* [here](log/file.md).

The configuration above is just nice example of how things work. In practice, you'll never have that simple configuration.

To add another configuration block, just add another item to `'log'` array. For example, let's say we want to send all
`debug` messages to file, but when we run [CLI script](cli.md), we want all these log messages visible in console:

```php
'log' => [
	[
		'enabled' => true,
		'adapter_class' => '\Koldy\Log\Adapter\File',
		'options' => [
			'log' => ['debug']
		]
	],
	[
		'enabled' => PHP_SAPI == 'cli',
		'adapter_class' => '\Koldy\Log\Adapter\Out',
		'options' => [
			'log' => ['debug', 'notice', 'info', 'warning', 'error', 'sql', 'critical', 'alert', 'emergency']
		]
	]
]
```

**Note** that second *writer* will be enabled only in `CLI` env. It'll be ignored for all HTTP requests. `Out` *writer*
simply prints all log messages to buffer, so don't ever use for HTTP requests.

Read more about `\Koldy\Log\Adapter\Out` [here](log/out.md).

**Next:** note that when you use `Log::debug()`, this message will go both to file and to `Out`.

On this example, you can see that log message can be sent to one or more **writers** easily.

You can have multiple `File` loggers with different configuration. Let's say you want to log all informative messages
like `debug`, `sql`, `notice` and `info` to file named `YYYY-MM-DD.log` and you want to log all problems from `warning`,
`error`, `emergency`, `alert` and `critical` to file named `YYYY-MM-DD.err`:

```php
'log' => [
	[
		'enabled' => true,
		'adapter_class' => '\Koldy\Log\Adapter\File',
		'options' => [
			'log' => ['debug', 'sql', 'notice', 'info']
			// file name by default is YYYY-MM-DD.log so nothing else is needed
		]
	],
	[
		'enabled' => true,
		'adapter_class' => '\Koldy\Log\Adapter\File',
		'options' => [
			'log' => ['warning', 'error', 'critical', 'alert', 'emergency'],
			'file_name_fn' => function () {
				$date = gmdate('Y-m-d');
				return "{$date}.err";
			}
		]
	]
]
```

You can clearly see distinction between first and second *writer*.

> Note that `file_name_fn` will be executed on every log message defined under `log` when running in CLI env. If the name
of file contains date or any other dynamic value, then value might change during execution. Imagine long running CLI script
that was started before midnight - date will change in meantime.

> In other case, `file_name_fn` will be executed only once on normal HTTP request even if midnight passes. It's made
this way due to performance reasons and for practical reasons. If you have log messages for the request that started
few milliseconds before midnight and you need to see all log messages from that request, you can see them all in one file,
instead of two files.

To be clear, every item from `'log'` array is one `writer` because it'll become the class instance of the mentioned
class name.

Now that you know how to combine logs, check available log writers.


## Available Log Writers

Here's the list of writers which framework supports. Check each writer for more options:

1. [\Koldy\Log\Adapter\File](log/file.md) - most common in development and other sites with not so huge traffic
2. [\Koldy\Log\Adapter\Out](log/out.md) - most common when running the [CLI scripts](cli.md)
3. [\Koldy\Log\Adapter\Db](log/database.md) - use this writer when you want to store log messages to database
4. [\Koldy\Log\Adapter\Email](log/email.md) - use this writer when you want to send log messages to email
- it's recommended to use it only for `critical`, `emergency` and similar message levels
5. [\Koldy\Log\Adapter\Other](log/other.md) - use this writer when you want to quickly handle log messages by yourself
- it's useful for sending log messages to Slack or any other 3rd party service


## Log Elements

There are few peaces of log data that makes the information useful:

1. **What** - this is message level (e.g. `debug`), one of the above
2. **When** - this is the [DateTime](http://php.net/manual/en/book.datetime.php) when you called any of the methods from the `Log` class
3. **Who** - this is usually information about currently logged user, *fallback information* otherwise
4. **Decription** - this is actual message(s) that you passed

As you already know, you're passing only description. All other data is automatically detected. If you digg deeper in
framework's source, you'll notice that each time you call log message, internally, instance of `\Koldy\Log\Message` is
created which carries all the mentioned information. Let's see what's what:


### *When*

This is time of message creation. It's instance of [DateTime](http://php.net/manual/en/book.datetime.php).


### *Who*

This information tells *who* in your system triggered this log message. Framework knows nothing about your system, so
you're responsible for this. By default, nothing is set, so *fallback information* is request's IP (in case of HTTP) or
server's IP (in case of CLI) with the random number, like `172.25.18.46-160334` or `127.0.0.1-636665`. Random number
is just 6-digit random integer. If you're having a lot of requests in the same second, time will not be enough to
link multiple log messages within the same request between multiple users. That's why random number is here - it's
generated once when everything starts.

To explicitly say who is triggering log messages, simply call:

```php
Log::setWho(string $who);
```




## Logging Messages

As already seen, every log message level has it's own method you can use:

```php
use Koldy\Log;

Log::debug('Something');
Log::sql('Something');
Log::notice('Something');
Log::info('Something');
Log::warning('Something');
Log::error('Something');
Log::alert('Something');
Log::emergency('Something');
Log::critical('Something');
```

It is possible to pass multiple parameters. Don't worry if you pass array or object:

```php
Log::debug('My name is', $name);
Log::debug('My settings are', $settingsArray);
```

Now that you know all [log elements](#log-elements), you're able to generate log message by creating the `Message`
instance directly in code:

```php
$message = new \Koldy\Log\Message('info', 'Something happened');
Log::message($message);
```

This is longer way of logging stuff, but you have greater control of it. You can even manually set time
of the `Message`. This is not recommended, so use it only in special cases when you really need this.


## Creating Custom Logger

To create custom logger, simply create class somewhere in library and extend `\Koldy\Log\Adapter\AbstractLogAdapter`.
When you implement all required methods, you'll be able to define your custom class as `adapter_class` in log config.
Really, that's all you have to do.