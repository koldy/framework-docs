# Project Structure

Project structure is very flexible and you can make any structure just by
setting [paths in index.php](configuration.md#paths).

Our boilerplates are shipped with the configuration of structure we're recommending and it looks like this:

```
[projectRoot]
	| application
	|	| controllers 
	|	| library
	|	| migrations
	|	| modules
	|	|	| name
	|	|	|	| controllers
	|	|	|	| library
	|	|	|	| scripts
	|	|	|	| views
	|	| scripts 
	|	| views 
	| configs
	| public
	|	| static
	| storage
	|	| cache
	|	| log
	|	| tmp
	| tests
	| tools
	| vendor
```

Project structure on github also contains `.gitignore` which completely ignores `storage`, `vendor` and other
similar folders. If you run our init scripts, `storage` will be generated, `vendor` and `bin` directory will
be created by Composer and etc.

In standard development, you'll probably have `node_modules` folder and some organisation for your frontend stuff.

## application

You should place all backend logic and other backend related stuff under `application` directory.

You can get full path to this directory using:

```
\Koldy\Application::getApplicationPath();
```

### controllers

Place all controller classes here. Controller classes should be named like `ContactController` with no namespace.
This directory is registered in include pth by framework, so you're able to extend some other controller.

You can get full path to this directory using:

```
\Koldy\Application::getApplicationPath('controllers');
```

### library

Place all other classes related for your project here. Library doesn't have namespace so you can organise it and
name it however you want. Since `library` directory has bigger priority in include path than other classes from
Composer, you need to be aware that you can easily "override" any class from `vendor` directory just by naming
it the same way.

We're recommending that you put here models for your tables in database.

Structure example:

```
library
	| App
	|	| Exception.php - namespace App, class Exception
	|	| Page.php - namespace App, class Page
	| Db
	|	| User.php - namespace Db, class User
	... and etc
```

### migrations

All database migrations will be placed here. Read more on [database migrations documentation](database/migrations.md).

### modules

Modules are logically separated parts of the system and each module can have its own `controllers`, `library`,
`views` and `scripts`.

You can think of them like "subsystem" to the main system where subsystem can "see" the main system, but main
system can't see the subsystem; and "subsystem" can't see other "subsystems", unless other "subsystem" is
registered in runtime.

Read [modules documentation](modules.md) for more details.

### scripts

Place any [CLI](cli.md) script in this directory.

Read [CLI documentation](cli.md) for more details.

## configs

This is directory where all configuration files should be placed, like `database.php`, `cache.php`, `session.php`
and `mail.php`. These config files are here because framework will look for them when you try to query a database,
try to write something in cache, try to use sessions or try to send an email.

If you have some project specific configuration, you should place those files here as well.

To use config files for yourself, read how on [Configs documentation](configs.md).

## public

This directory is **directory root** for your virtual host. It contains `index.php` and this is entry point for
every HTTP request and for every script you run from command line.

Framework requires only `index.php` to be in this directory. Everything else is up to you, put your assets, fonts
and other files here for direct access through web server without entering in your application.

You can get full path to this directory using:

```
\Koldy\Application::getPublicPath();
```

### static

We're recommending that you create directory named `static` inside of `public` and place all your static files
there. In real world when you need to scale or optimise your backend, you'll probably need to move assets to other
domain or location, so hardcoding paths to assets is not recommended. Simply by using `\Koldy\Route::asset()` method,
you can avoid later problems when your assets are moved else where.

Our boilerplate has this directory and [assets](configuration.md#assets) are configured to use that directory.

You can find more details on [assets documentation](configuration.md#assets).

## storage

This directory should contain all files generated by your app. This is why it's git ignored by default.

Fore example, framework's file caching will use `storage/cache` directory to place files if you don't specify other
path in configuration. Same is with logging to file, it'll go to `storage/log` if path is not set in configuration.

You can get full path to this directory using:

```
\Koldy\Application::getStoragePath();
```

## tests

This is directory where you should place all your unit tests. Our boilerplate is configured to work
with [PHPUnit](https://phpunit.de) and we ship basic unit test which is example from their documentation.

You can start tests by running `./test.sh` in your project root.

## tools

This is directory where you can place all your tools for development. We ship configurations for Vagrant provisioning
and that's what allows us to setup dev environment on your computer by running single command.

## vendor

This directory is automatically created by [Composer](https://getcomposer.org) and you won't be able to run
anything without this.