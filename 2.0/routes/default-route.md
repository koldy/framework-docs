# Default Route

## Introduction

DefaultRoute class is good for all size projects, from small to large projects, from public pages,
administration interfaces to API backends.

It supports:

1. Modules and controllers and actions in each module
2. Application (root) controllers and actions
3. Two modes: Ajax/Action and RESTful mode on all controllers and actions
4. Exception handling

There is no configuration file where you need to map route with the class and action. Instead, DefaultRoute
will look for controller and action based on REQUEST_URI and configuration.


## Configuration

As already mentioned in [mandatory config](../configuration.md#routing-class-and-routing-options),
DefaultRoute accepts one parameter from **routing_options**:

1. `always_restful` - `true` or `false`, if not present, default is `false`

If you want RESTful ability per controller and not on all routes, then keep `always_restful` on `false`,
and define static `$resftul` to `true` in controller itself, like this:

```php
class ApiController
{
    public static $restful = true;
}
```


## RESTful Mode Always ON

If `always_restful` is set to `true`, then class will differently construct action (methods) names in
controller depending on request **method**. Supported HTTP methods are `GET`, `POST`, `PUT` and `DELETE`, so you're
able to map the same REQUEST_URI in four different ways, depending on the HTTP method.

For example, if you make `POST=/users/list`, DefaultRoute will

1. First look up for `users` module.

If `users` module exists, it'll then look up the following controllers
and methods in `users` module in exactly this order:

1. `ListContoller->postIndex()`
2. `ListController->__call()`
3. `IndexController->postList()`
4. `IndexController->__call()`

Other case:

2. `users` module doesn't exist.

DefaultRoute will then inspect root controllers (in `[applicationPath]/controllers`):

1. `UsersContoller->postList()`
2. `UsersController->__call()`
3. `IndexController->postUsers()`
4. `IndexController->__call()`


In both cases, if none of the controllers nor methods are found, DefaultRoute will throw `\Koldy\Response\Exception\NotFoundException`.


## RESTful Mode Always OFF

In this mode, you're able to map the same REQUEST_URI in only two different ways called `Action` and `Ajax`.

`Ajax` requests are all methods which have the following conditions met:

1. HTTP method must be `POST`
2. Header `X-Requested-With` must be present and have the `xmlhttprequest` value

Most of the client side Javascript frameworks and libraries (such as jQuery) automatically adds that header, but in case you're using
ajax library that doesn't do that automatically, you'll need to set it yourself.

If request is not *Ajax* request, then it's `Action`.

For example, if you make `GET=/users/list`, DefaultRoute will

1. First look up for `users` module.

If `users` module exists, it'll then look up the following controllers
and methods in `users` module in exactly this order:

1. `ListContoller->indexAction()`
2. `ListController->__call()`
3. `IndexController->listAction()`
4. `IndexController->__call()`

Other case:

2. `users` module doesn't exist.

DefaultRoute will then inspect the same controllers, but within the application root controllers (in `[applicationPath]/controllers`):

1. `UsersContoller->listAction()`
2. `UsersController->__call()`
3. `IndexController->usersAction()`
4. `IndexController->__call()`


If this was `Ajax` request, then all method names would have the `Action` suffix instead of `Ajax`.

As mentioned in [configuration](#configuration) above, if you put `public static $restful = true;` in your controller, then
naming of the methods within that class will be the same as if `always_restful` was set to `true`.


## Before And After methods

DefaultRoute class respects `before()` and `after()` methods in controller classes. If you define `before()` method in controller
and `before()` method doesn't return anything or returns `NULL`, DefaultRoute will continue with the execution of targeted method,
meaning you're able to *override* response before executing method. This is useful if you want to check the user session and if session
doesn't exists or has expired, you can return [Redirect](../response/redirect.md) response from `before()` method.

The `after()` method does the same, except it's executed after response from targeted method is dumped to output buffer, meaning
that return value from `after()` method is ignored.


## Error Handling

If DefaultRoute fails to execute your method, it'll throw `\Koldy\Response\Exception\ServerException`. If
controller or action (method) is not found after all attempts, DefaultClass will throw `\Koldy\Response\Exception\NotFoundException`.

To catch these exceptions, you can define class `ExceptionHandler` that extends `\Koldy\Response\ResponseExceptionHandler` in
`[applicationPath]/ExceptionHandler.php` and then override any or all methods from the extended class.

If you want to have exception handler per module, then do the same, just put `ExceptionHandler` class within the controllers in the
module where you want to achieve overriding.


## Using Default Route

Although there's `\Koldy\Route` class which is helper for routes, it might behave differently depending on which routing
class you're using. The following examples will work for DefaultRoute.

To get the routing class instance, use:

```php
use Koldy\Route;
use Koldy\Route\DefaultRoute;

$route = Route::getRoute();
echo Route instanceof DefaultRoute ? 'YES' : 'NO'; // will print YES
```

`\Koldy\Route` class has some useful helpers. Imaging you're calling these methods under REQUEST_URI
`/users/list/page/2?print=no` with no `users` module:

```php
Route::controller(); // returns 'users'
Route::isController('users'); // returns true

Route::action(); // returns 'list'
Route::isAction('list'); // returns true

Route::is('users', 'list'); // returns true
Route::isModule('users'); // return false

Route::getVar(2); // returns 'page' because it's 3rd "variable" in (users, list, page, 2)
Route::getVar('print'); // returns 'no'

Route::href('users', 'list', ['page', '2', 'print' => 'no']); // returns "/users/list/page/2?print=no"
```



## Routes To Other Sites

If you're having multi-site configuration, then you'll need another configuration file `configs/sites.php` which is
array of other sites where key is the name of the site you'll be using and value is domain with schema to other site. 

Let's say you have this sites in `configs/sites.php`:

```php
return [
	'web' => 'https://my.site.com',
	'api' => 'https://api.site.com'
];
```

You can then use:

```php
Route::siteHref('api', 'users', 'list', ['page', '2']); // returns "https://api.site.com/users/list/page/2"

Route::site('api', '/users/page/2'); // returns "https://api.site.com/users/page/2"
```


## Routes To Assets

To have the proper routes to assets, first properly configure [mandatory configuration](../configuration.md#assets).
Then, when you know the keys from that config, you can simply use:

```php
echo Route::asset('images/logo.png', 'dev'); // returns "http://localhost:3000/static/images/logo.png"
```
