# Change Log

This is first version of the framework where new PHP 8.1 features are implemented. If you have used Koldy Framework before, then
read the change log so you can get familiar with the changes. All changes can be applied in your project with simple search&replace
rules and everything else is back compatible.

- Koldy\Version class is removed
- Route::getVar() is not using default value through an (2nd) argument any more
- Request class: get(), post(), put() and delete() methods are not accepting default (2nd) parameter any more
- Request class: get(), post(), put() and delete() methods are not accepting allowed values through an argument any more
- method \Koldy\Config->get() doesn't accept default value through an argument any more
- \Koldy\Config->getArrayItem() doesn't accept default value through an argument any more
- \Koldy\Db\Model::save() now returns null instead of -1 if DB was not queried
- CreatedAt trait - method setCreatedAtDateTime() method doesn't exist any more; setCreatedAt() now accepts string|DateTime instead and it doesn't return static/$this
- DeletedAt trait - methodsetDeletedAtDateTime() method doesn't exist any more; setDeletedAt() now accepts string|DateTime instead and it doesn't return static/$this
- UpdatedAt trait - methodsetUpdatedAtDateTime() method doesn't exist any more; setUpdatedAt() now accepts string|DateTime instead and it doesn't return static/$this
- \Koldy\Http\Request now accepts instances of CURLFile in params
- removed deprecated method \Koldy\Db\Insert::table()
- Pagination class is removed
- PHPMailer class is removed from the framework - instead, use [koldy/phpmailer](https://packagist.org/packages/koldy/phpmailer) package

## Migrating your project(s) from older versions

To make your Koldy project(s) compatible with this version, you can follow the rules for code changes:

- in all DB models, replace **protected static $adapter** with **protected static string | null $adapter**
- in all DB models, replace **protected static $table** with **protected static string | null $table**
- in all DB models, replace **protected static $primaryKey** with **protected static string | array $primaryKey**
- in all DB models, replace **protected static $autoIncrement** with **protected static bool $autoIncrement**
- although this is PHP 8.1 thing, in all classes, replace **public function \_\_toString()** with **public function \_\_toString(): string**
- although this is PHP 8.1 thing, in all classes, replace **public function jsonSerialize()** with **public function jsonSerialize(): mixed**
- check all Koldy config calls for default values and remove 2nd argument (the default param) if you have used it
