# Getting Started

This documentation assumes that you have the knowledge of PHP syntax, basics about relational databases
and MVC. If you need to google any of the words in this block, then you're probably not on the right place.

## Requirements

The system itself has the following requirements:

* PHP >= 8.1
* OpenSSL PHP extension
* PDO PHP extension

If you're using Linux Ubuntu or Debian, you can use this:

```bash
apt-get install php8.1-fpm php8.1-cli php8.1-common php8.1-mysql php8.1-mbstring php8.1-pgsql php8.1-sqlite php8.1-gd php8.1-curl php8.1-bcmath php8.1-xml
```

## Change Log

This is first version of the framework where new PHP 8.1 features are implemented. If you have used Koldy Framework before, then
read the change log so you can get familiar with the changes. All changes can be applied in your project with simple search&replace
rules and everything else is back compatible.

- Koldy\Version class is removed
- Route::getVar() is not using default value through an (2nd) argument any more
- Request class: get(), post(), put() and delete() methods are not accepting default (2nd) parameter any more
- Request class: get(), post(), put() and delete() methods are not accepting allowed values through an argument any more
- Pagination class is deprecated and you really shouldn't use it any more
- method \Koldy\Config->get() doesn't accept default value through an argument any more
- \Koldy\Config->getArrayItem() doesn't accept default value through an argument any more
- \Koldy\Db\Model::save() now returns null instead of -1 if DB was not queried
- CreatedAt trait - method setCreatedAtDateTime() method doesn't exist any more; setCreatedAt() now accepts string|DateTime instead and it doesn't return static/$this
- DeletedAt trait - methodsetDeletedAtDateTime() method doesn't exist any more; setDeletedAt() now accepts string|DateTime instead and it doesn't return static/$this
- UpdatedAt trait - methodsetUpdatedAtDateTime() method doesn't exist any more; setUpdatedAt() now accepts string|DateTime instead and it doesn't return static/$this
- \Koldy\Http\Request now accepts instances of CURLFile in params
- removed deprecated method \Koldy\Db\Insert::table()

After all these minor changes, to make your Koldy project(s) compatible with this version, you can follow these rules for code changes:

- in all DB models, replace "protected static $adapter" -> "protected static string | null $adapter"
- in all DB models, replace "protected static $table" -> "protected static string | null $table"
- in all DB models, replace "protected static $primaryKey" -> "protected static string | array $primaryKey"
- in all DB models, replace "protected static $autoIncrement" -> "protected static bool $autoIncrement"
- although this is PHP 8.1 thing, in all classes, replace "public function \_\_toString()" -> "public function \_\_toString(): string"
- although this is PHP 8.1 thing, in all classes, replace "public function jsonSerialize()" -> "public function jsonSerialize(): mixed"
- check all Koldy config calls for default values and remove 2nd argument (the default param) if you have used it

## Development Environment

Setting up all technologies you need on your computer can take a lot of time. To speed up this process, we
recommend [Vagrant](https://www.vagrantup.com) virtual machine which makes the whole process much easier.

The basic boilerplate you can use is [boilerplate](https://github.com/koldy/boilerplate) which is project ready for you
to test or start your own project. To get it, run the following command in your terminal:

```
{{frameworkBoilerplateCloneCommand:3.0}} && cd boilerplate && ./init.sh
```

After it's done, open http://localhost:5000

Vagrant VM will install PHP 8.1, Postgres, MySQL and Nginx. It'll adjust configuration for development and it'll put web server
and PHP logs in `logs` folder in your project, so it's easier to troubleshoot things during development.

## Installation

Koldy is shipped with `DefaultRoute` class that requires your web server to handle URL rewrites.

##### Apache

Koldy ships `public/.htaccess` file for Apache web server. Be sure to enable `mod_rewrite` in your
Apache configuration so the `.htaccess` file gets included in its configuration.

##### Nginx

If you're using Nginx, put the following directive to your Nginx configuration:

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```
