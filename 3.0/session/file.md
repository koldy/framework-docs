# Session File Storage

To store *one file per session* on file system, use `\Koldy\Session\Adapter\File` class. Options array accepts only
`session_save_path` which should be the full path to the folder where session files should be written. If this is not
defined, then `/storage/session` folder will be used. Final config example should look like this:

```php
return [
	'adapter_class' => '\Koldy\Session\Adapter\File',
	'options' => [
		'session_save_path' => '/var/tmp/session'
	],

  'cookie_life' => 0,
  'cookie_path' => '/',
  'cookie_domain' => '',
  'cookie_secure' => false,
  'session_name' => 'koldy',
  'http_only' => true
];
```

If folder you set doesn't exists, framework will try to create it while trying to write session file. If it fails to
create the folder, `\Koldy\Filesystem\Exception` will be thrown.

> **CAUTION!** If you have high traffic site, then using file storage won't be good idea because PHP will constantly try
to read and write on filesystem. Instead of file driver, consider using [database](database.md) or some other
session adapter.

&nbsp;

> **Are you behind load balancer?** File session storage can work on cloud only if you have load balancer that will persist session
on incoming requests. This means that all requests for end user has to be routed to the same web node.


[&larr; Back To Session](../session.md#session-storage-configuration)
