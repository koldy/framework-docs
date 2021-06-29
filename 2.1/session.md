# Session


## Introduction

In most of the projects you'll ever do, you'll need to store something to user's session. Framework's `Session` class
implements standard PHP [session methods](http://www.php.net/manual/en/book.session.php) on smart way, so you don't have
to worry about things like, *did I start the session* or *was the session closed or not*. Everything you need, `Session`
class has.

To use all examples from below, you should:

```php
use Koldy\Session;
```


## Configuration

```php
return [
	/*
	'adapter_class' => '\Koldy\Session\Adapter\Db',
	'options' => [
		'connection' => null,
		'table' => 'session'
	],
	*/
	'cookie_life' => 0,
	'cookie_path' => '/',
	'cookie_domain' => '',
	'cookie_secure' => false,
	'session_name' => 'koldy',
	'http_only' => true
];
```

Session class requires another configuration file located in `/configs/session.php` where you can configure everything
related sessions, like the cookie settings or where you want to store session information on server.

Boilerplate we ship will always use PHP's default session settings, which is *one file per session* philosophy. If your
project gets higher traffic, you'll need to think about moving session storage from files to something faster, like
database or temporary in-memory storage. Database storage is often used when you're running your project behind load
balancer and you want to centralise session information.


### Session Storage Configuration

Our `session.php` configuration has `adapter_class` block commented our, which means that framework will use default
PHP's session configuration (the one you have defined in your `php.ini`). If you want to override that, uncomment the
commented block and define `adapter_class` with the class name that implements PHP's
[SessionHandlerInterface](http://www.php.net/manual/en/class.sessionhandlerinterface.php). Any other class defined here
will be rejected and `\Koldy\Session\Exception` will be thrown.

Framework ships two possible adapter classes that implements
[SessionHandlerInterface](http://www.php.net/manual/en/class.sessionhandlerinterface.php). Choose:

1. [\Koldy\Session\Adapter\File](session/file.md) - simple file storage, but with customised storage path
2. [\Koldy\Session\Adapter\Db](session/database.md) - implementation that stores session information to database


### Creating Custom Session Storage

To create custom session storage, simply create class anywhere in your project (we recommend `library`) and implement
[SessionHandlerInterface](http://www.php.net/manual/en/class.sessionhandlerinterface.php). Then, go to
`/configs/session.php` and set your own class under `adapter_class`. Really, that's it.


### Cookie Configuration

To configure session's cookie, adjust standard cookie settings in `/configs/session.php`.


## Starting Session

To start the session, use:

```php
Session::start();
```

This method is safe and you can call it multiple times and nothing will break. If you need to start session by
knowing the session ID, then just pass session ID to `start()` method.

To check if session has started, use `\Koldy\Session::hasStarted()`.


## Storing Data To Session

```php
Session::set($key, $value);
Session::add($key, $value);
```

`set()` method will always store the value to session, while `add()` method will store value only if it doesn't already
exist.


## Retrieving Data From Session

```php
Session::get($key);

Session::has($key);

Session::getOrSet($key, function () {
	return 'value if $key does not exists';
});
```

To get the value from session, pass key name to `get()` method. If key doesn't exist, `NULL` will be returned.

To check if key exists in session, use `has()` method.

Common case for data in session is to get the value and set the value if value was't yet stored. To do that, use
`getOrSet()` method where first parameter is the key name, and second parameter is the `Closure` that must return value
you plan to store.


## Deleting Data And Destroying Session

```php
Session::delete($key);
Session::destroy();
```

To delete one key from session, use `delete()` method. To delete complete session, use `destroy()` method. Destroy method
will use both PHP native [session_unset()](http://php.net/manual/en/function.session-unset.php) and
[session_destroy()](http://php.net/manual/en/function.session-destroy.php) functions.

If you call `destroy()` method without previously starting the session, be aware that framework will start the session
automatically before destroying it.


## Closing Session

Closing session means you don't plan to store anything anymore to the currently open session. It writes session data and
ends session.

```php
Session::close();
```

To check if session has closed, use `Session::isClosed()`.