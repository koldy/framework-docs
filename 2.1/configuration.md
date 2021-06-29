# Configuration

To configure your project properly, basic configuration has to be set on entry point:
[public/index.php](#mandatory-index-php). This is mandatory config which tells the framework what is where.

All other configuration files are located in [configs directory](project-structure.md#configs). 


## Mandatory - index.php

This configuration is used on every HTTP request and [CLI](cli.md) call and it's mandatory for system to work. Here's
what every item in `index.php` means.


### site_url

This is domain with schema or array of domains with schema. Schema has to be defined so framework can know if you're
using SSL or not. Remember that this is also used in [CLI](cli.md) where domain can't be automatically detected.

| example value | meaning |
| --- | --- |
| `https://example.com` | You're using domain *example.com* with SSL |
| `http://localhost:3000` | You're using domain *localhost:3000* without SSL |

You can get these values by using:

| Class and method | return value |
| --- | --- |
| `Application::getDomain()` | You'll get domain part only, like `example.com` or `localhost:3000` |
| `Application::isSSL()` | Returns `true` if `https` is detected, `false` otherwise |
| `Application::getDomainWithSchema()` | Returns full schema and domain in use, like `https://example.com` or `http://localhost:3000` |
| `Route::home()` | This is alias to `Application::getDomainWithSchema()` |


### assets

This is key-value array with the list of other domains or locations where your assets are located. This is useful if
you're using CDN, or you simply want to have different locations for development and production environment.

| key | value | meaning |
| --- | --- | --- |
| `static` | `/static` | You placed your assets in public folder relative to the domain set under `site_url` |
| `cdn` | `https://cdn.example.com` | Your assets are placed behind CDN |
| `dev` | `http://localhost:3000/static` | Your assets can be reached locally |

You can get the path to your assets using `Route::asset()`, where first parameter is the file name with or without
additional sub-location and second parameter is optional key from the config above. If second parameter is not
provided, framework will use the first item from `assets` array.

Example:
```
\Koldy\Route::asset('logo.png'); // returns https://example.com/static/logo.png
\Koldy\Route::asset('images/logo.png'); // returns https://example.com/static/images/logo.png

\Koldy\Route::asset('logo.png', 'cdn'); // returns https://cdn.example.com/logo.png
\Koldy\Route::asset('logo.png', 'dev'); // returns http://localhost:3000/static/logo.png
```

Final configuration in `index.php` would look like:

```
'assets' => [
	'static' => '/static',
	'cdn' => 'https://cdn.example.com',
	'dev' => 'http://localhost:3000/static'
]
```


### paths

This gives you ability to make your own structure of the project or when needed in some special cases, some of the
folders can be placed on totally different file system location outside of the project.

| key | meaning | default |
| --- | --- | --- |
| `application` | Path to your application directory where by default is all other backend stuff | `[projectRoot]/application` |
| `storage` | Path to storage directory where usually are all files generated on the server. This directory is ignored by `.gitignore` by default. | `[projectRoot]/storage` |
| `view` | Path to your templates (or so called views) | `[projectRoot]/application/views` |
| `modules` | Path to your modules directory | `[projectRoot]/application/modules` |
| `scripts` | Path to your CLI scripts | `[projectRoot]/application/scripts` |
| `configs` | Path to directory where all config files are located| `[projectRoot]/configs` - UPDATE FW |


### config

This gives you ability to override location of your configs per file.

Example: You might have a config file named `site-conf.php` in `configs` folder for your development environment and
you're using it with `Application::getConfig('site-conf')` which is fine. In this case, framework will look for the
config file on `[projectRoot]/configs/site-conf.php` by default. But, when your app is deployed on production server,
you might want to read this config from a location outside of project root. In this case, you can define `site-conf`
key with value like `/var/configs/site-conf.php` and framework will do the rest.

Final config would look like:

```
'config' => [
    'site-conf' => '/var/configs/other-conf.php'
]
```


### env

This tells framework weather you're running in `development` or `production` mode. Note that there's no `testing`
environment. More on that read under [live](#live) section.

This setting is usually used on cases like error handling. Error details and stacktrace will be exposed during
development so you can see error right away, while it won't be exposed on production environment.

| value | constant | meaning |
| --- | --- | --- |
| `0` | `Application::DEVELOPMENT` | App is running in development environment |
| `1` | `Application::PRODUCTION` | App is running in production environment |

Final config example:

```
'env' => \Koldy\Application::PRODUCTION
```

Usage:

| method | meaning |
| --- | --- |
| `Application::inDevelopment()` | Returns `true` if `0` is set for `env` |
| `Application::inProduction()` | Returns `true` if `1` is set for `env` |

If you're using Webpack, Gulp, Grunt, Bower or any other package manager, you'll probably want to handle paths to
assets differently for development and differently on production server.


### live

This is boolean variable that tells if you're running this system live or not. Framework internally doesn't use this
setting, but you should.

In standard development, you'll need to implement 3rd party tracking code, like Google Analytics, Intercom and such,
but you'll want to hide tracking code on all other systems other than live system.

Final config example:

```
'live' => true
```

To use this setting, use `Application::isLive()` which returns `true` or `false`.


#### Testing environment

If you're looking for `testing` environment, you can get it by combining `live` and `env`. This is how we see it:

| env | live | meaning |
| --- | --- | --- |
| `development` | `false` | You're running this system locally for development |
| `development` | `true` | You're running system locally, but including "live" stuff. This can be temporary, just for testing to see if you implemented everything correctly |
| `production` | `false` | You are running in production environment, but not live - this is `testing` because you're testing production build, but not using any tracking scripts |
| `production` | `true` | You are running fully live on your production server |


### timezone

This is standard timezone string from [PHP](http://php.net/manual/en/timezones.php). Put the timezone you need.
Default is `UTC` and it's required.

In standard development when working with multiple timezones, all dates and times are stored in database or other
permanent storage in UTC and when you need to show the time to user, it is then converted to the user's local timezone.


### additional_include_path

As it says, this is array of additional include paths. This is useful if you're having multiple projects and you want
to access directories on other locations or you have some *library* project outside of your project and you're
depending on it.

Final config example:

```
'additional_include_path' => [__DIR__ . '/../../library']
```


### key

This is random key with the maximum length of 32 characters that you should generate. Make it unique.

It is used when framework needs some uniqueness per project. For example, if you're using `Crypt` class and you try
to encrypt and decrypt something without providing a key, framework will then use this key as its `key` for encrypting.


### routing_class and routing_options

This is class name of routing class that will be used for resolving HTTP requests and translating it into controllers
and methods. The class name has to extend `\Koldy\Route\AbstractRoute` in order to make this work.

Framework has `\Koldy\Route\DefaultRoute` class and there's 99% chance that it will suit all your needs just fine.

`routing_options` is array that will be passed to constructor of `routing_class` as first parameter.

Final config example:

```
'routing_class' => '\Koldy\Route\DefaultRoute',
'routing_options' => [
	'always_restful' => false
],
```

In our case, `DefaultRoute` accepts only `always_restful` setting. If you create your own routing class, then you can
set the array of any structure here. Read more on [DefaultRoute docs](default-route.md).


### security

This block contains some common security stuff, such as OpenSSL default method and CSRF settings. Example:

```
'security' => [
	'openssl_default_method' => 'aes-256-cbc',

	'csrf' => [
		'enabled' => false,
		'parameter_name' => 'csrf',
		'cookie_name' => 'csrf_token',
		'session_key_name' => 'csrf_token'
	]
]
```

Find more info about OpenSSL methods [here](https://secure.php.net/manual/en/function.openssl-get-cipher-methods.php).

Find more details about CSRF implementation [here](csrf.md).

### filesystem

This is array of some file system specific work. So far, only default chmod setting should be defined here. Example:

```
'filesystem' => [
	'default_chmod' => 0755
]
```


### log

This is array of possible log *writers*. Think of it like this: when you use `Log::debug('something');`, where should it
go? Should it end up in file on filesystem, database, in console, Slack or somewhere else? All log options should be
configured here.

Read more about Log [here](log.md).


## Alternative Location Of Mandatory Configuration

Having all mandatory configuration in `index.php` is good for performance reasons - you eliminate loading configuration
from other file on literally every execution. In some rare cases, you'd want to have this configuration in separate
file, usually when you have multiple entry points (entry points that aren't going through index.php).

In this case, whole configuration array you're passing to `useConfig()` should be moved to `configs/application.php`:

```php
return [
	'site_url' => 'https://domain.com',
	...
];
```

Then, instead of passing array to `useConfig()`, simply pass location of `application.php` file. If `application.php` is
within `configs` folder, you can use:

```php
Koldy\Application::useConfig(__DIR__ . '/../configs/application.php');
```