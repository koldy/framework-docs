# Modules

Think of modules like logically separated parts of the system. Each module can have its own `controllers`, `library`,
`views` and `scripts`. Controllers in modules are named the same way
as [application/controllers](project-structure.md#controllers), without namespace. And yes, you can potentially have
the same class name across your project and it is possible because module has priority over the rest of the application
and it ["overrides" the "namespace"](#controllers) from the app.

It means that include path for module's controllers and module's library comes before the include path of the rest of
the `application`. `vendor` has the lowest priority.

When framework is "running" controller from [application/controllers](project-structure#controllers), you're not
able to access any module unless module is registered. The same is when framework is "running" controller class from
module, you can't access other module's controllers nor library unless you use:

```
\Koldy\Application::registerModule($name);
```

But module can access everything from application's [controllers](project-structure.md#controllers),
[library](project-structure.md#library) and [views](project-structure.md#views).

Long story short:

1. if you're in the module, you see yourself and the main application
2. if you're in the main application, you don't see any module unless module is registered
3. if you're in the module, you don't see other modules unless other module is registered

In some rare cases, when registering module, you might want to initialise some stuff, like loading some custom and
totally different configuration needed just for this module to work. In this case, you can place `init.php` in module's
directory, like `modules/admin/init.php`. You can place any code in here and it'll be executed every time module
is registered.

## Real World Example

> Assumption: you're using [DefaultRoute](default-route.md) for your [routing_class](configuration.md#routing-class-and-routing-options).

Let's say you have a public website with a lot of sub pages, like contact page, product list, product details, search,
comments, small user area and etc. And now, you want to add to your site administration area and you want to provide
basic API for your users. That would be two modules: `admin` and `api`.

Yes, you can simply create `AdminController` in [application/controllers](project-structure.md#controllers) where you
can handle all HTTP endpoints but then your `AdminController` will probably have few or several thousand of lines of
code and that's not something we (developers) like. What if some other guy has to continue maintaining your code and
that guy is psychopath who knows where you live? Nobody wants that. This framework allows you not to have class files
with thousands of code.

Everything for public web can be placed in [application/controllers](project-structure.md#controllers) and all models
and other logic can be placed in [application/library](project-structure.md#library) because this is majority of the
site. To create a module, simple create subdirectory in `modules` named `admin` and `api` so the structure looks like this:

```
modules
	| admin
	|	| controllers
	|	| library
	|	| scripts
	|	| views
	| api
	|	| controllers
	|	| library
	|	| scripts
	|	| views
```

Administration area usually have its own look, some special tables in database, like administrator sign in credentials
and similar, but when you're signed in, you want to manage stuff from the public web site.

### controllers

When using [DefaultRoute](default-route.md), module named `admin` will "take" the URI namespace, so every request
that goes to `http://your-domain.com/admin` will end up in your `admin` module. If `admin` module doesn't exist,
framework will look up for `AdminController` class in [application/controllers](project-structure.md#controllers).
That's the "namespace overriding" mentioned on the top of this page.

### library

When mapping database tables from public web site into models, we're recommending that you place those files under `Db`
namespace in [application/library](project-structure.md#library).

When mapping module specific database tables, we're recommending that you place those files under module's `Db`
namespace. Example:

```
modules
	| admin
	|	| controllers
	|	| library
	|	|	| Db
	|	|	|	| AdminUser.php - namespace Db, class AdminUser
	|	| scripts
	|	| views
```

And now, if you try to use `\Db\AdminUser` in your `admin` in controllers, framework will first look it up on
`modules/admin/library`. If class is not there, it'll then continue to look it up
on [application/library](project-structure.md#library).

If you're in [application/controllers](project-structure.md#controllers) and you try to use `\Db\AdminUser`, you'll
get exception telling you class can't be found. If you really have to access this class from
[application's controllers](project-structure.md#controllers), then register your module before using `\Db\AdminUser`:

```
\Koldy\Application::registerModule('admin');
```

This is not recommended because it signalizes that you might have an architectural problem in your project.

### scripts

Place module specific [CLI](cli.md) scripts in this directory. For example, if you have script
`modules/admin/scripts/backup.php`, you can run it with:

```
./koldy run admin:backup
```

You can find more info on [Running CLI scripts in modules](cli.md#running-cli-scripts-in-modules).

### views

We mentioned that administration area usually have its own look. That's why you have `views` folder. Place your admin
specific view templates in this directory. If you created template file on `modules/admin/views/base.phtml`, then you
can simply use it like:

```
\Koldy\Response\View::create('admin:base');
``` 

Unlike [library](#library), module's views are accessible from application or any other module because when creating
view, you have to prefix it with the module name. You have to prefix it even if you're in the module's controller.
