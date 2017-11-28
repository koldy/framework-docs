# Configs

Framework is using configs for its own stuff, like database connections, cache adapters, mail adapter, session settings and etc. and we've noticed we have two types of configuration file structure that we call [simple config](#simple-config) and [pointer config](#pointer-config).

You can use both types for your own configuration files.

## File Location

To create your own configuration file, the easiest way is to put PHP file in configs directory. Beware that directory location for configs is set in [configuration](configuration.md) under [paths](configuration.md#paths). However, sometimes you might want to make exception for one config file's location only. In this case, you should override location for that file only under [config](configuration.md#config) key.

Creating your own configuration file is easy. Depending on what you need, you can write *simple* PHP return array statement with any structure you want, or you can use *pointer* file config.

## Simple Config

For example, you can put this `site-conf.php` to your configs directory.

```
<?php

return [
    'site_title' => 'My cool site',
    'google-analytics-id' => 'xxx123abc',
    'supporting' => [
        'api' => 'Yes.',
        'mobile-app' => 'Partially',
        'integration' => 'In development.'
    ]
];
```

You can get your config by calling `Application::getConfig('site-conf')`. It returns instance of `Config` or throws exception if there's something wrong.

```
$config = \Koldy\Application::getConfig('site-conf');
```

Usage:

| Call | return value |
| --- | --- |
| `$config->name()` | returns `site-conf` |
| `$config->getFullPath()` | returns full path on filesystem to the file from which config was loaded |
| `$config->getData()` | returns whole array from configuration file |
| `$config->get('site_title')` | returns `My cool site` |
| `$config->get('google-analytics-id')` | returns `xxx123abc` |
| `$config->has('car-model')` | returns `false` because it doesn't exists in config |
| `$config->getArrayItem('supporting', 'api')` | returns `Yes.` |
| `$config->get('supporting')` | returns array under key `supporting` |


## Pointer Config

Unlike *simple* config files, pointer config file is array of blocks of config where key can point to another block. The best example of this is database config where you can have more than one configuration blocks when connecting to multiple database servers. The `key` of the block is database connection's name. If you don't specifically say which connection you're using, framework will use 1st key from config. If key has the string for its value, then value will be used as key to next block. So, if you place in your config something like `'default' => 'db2'` on first place and then you try to grab the database connection without specifying name or you want to use `default` connection, framework will actually search for `db2`.

Here's the real world example. Imagine this is `database.php` in your configs directory.

```
<?php

return [
	'default' => 'admin',
	'website' => [
		'type' => 'mysql',
		'host' => '127.0.0.1',
		'port' => 3306,
		'username' => 'vagrant',
		'password' => 'vagrant',
		'database' => 'vagrant',
		'persistent' => true
	],
	'admin' => [
		'type' => 'postgres',
		'host' => '127.0.0.1',
		'port' => 5432,
		'username' => 'vagrant',
		'password' => 'vagrant',
		'database' => 'vagrant',
		'persistent' => true,
		'schema' => 'public'
	]
];

```

You can get your config now as:

```
$config = \Koldy\Application::getConfig('database', true);
```

Second parameter tells the framework it is pointer config. Now you can use your config like:

| Call | return value |
| --- | --- |
| `$config->get('default')` | returns array under `admin` key |
| `$config->get('admin')` | returns array under `admin` key |
| `$config->get('db2')` | throws exception because `db2` doesn't exist |

You may have up to 10 "redirects".

