# File System

PHP already provides great functions and classes for [filesystem manipulation](http://php.net/manual/en/book.filesystem.php),
so this framework's helper will provide some enhanced methods.


## Read Directory

You can simply read all files and folders within by using `\Koldy\Filesystem\Directory::read()` method, where first parameter is
the directory you want to read and second parameter is optional regex. If you pass regex pattern, only files that match
the patter will be returned.

Beware that this method returns `key => value` array, where key is the full path of file on file system, and value is
file name with extension or directory name. 

```
use Koldy\Application;
use Koldy\Filesystem\Directory;

$publicFiles = Directory::read(Application::getPublicPath());

print_r($publicFiles);
```

The example above will print all files and folders within the public directory and the output will look similar to this:

```
Array
(
    [/vagrant/public/favicon.ico] => favicon.ico
    [/vagrant/public/index.php] => index.php
    [/vagrant/public/static] => static
)
```

Beware that `read()` method is not recursive.

If you pass second parameter, like this:

```
$publicFiles = Directory::read(Application::getPublicPath(), '/\.php$/');
```

Then you'll get back filtered list of only PHP files.


## Read Only Files In Directory

To read only files in the directory, use `\Koldy\Filesystem\Directory::readFiles()` with the same parameters as
[\Koldy\Filesystem\Directory::read()](#read-directory) method has.


## Read Files Recursive

To read only files in the directory and all files within the sub-directories, use
`\Koldy\Filesystem\Directory::readFilesRecursive()` with the same parameters as
[\Koldy\Filesystem\Directory::read()](#read-directory) method has.


## Create Directory

`\Koldy\Filesystem\Directory::mkdir()` is using PHP's [mkdir()](http://www.php.net/manual/en/function.mkdir.php)
function to create directory, but with small difference.

First parameter is path of target directory and second parameter is optional chmod value, like `0755`. If second
parameter is not passed, framework will take the default value from [main configuration](configuration.md#filesystem).
If the setting is not set in main config, then `0644` will be used.

The `mkdir()` method in `Directory` class will always create directories recursively, so you don't have to worry about it.

If target directory already exists, nothing will happen.

In case when target directory can't be created, method will throw `\Koldy\Filsystem\Exception`.


## Deleting Directory

To delete directory and all the content inside recursively, use:

```
\Koldy\Filesystem\Directory::rmdirRecursive('/full/path/to/dir');
```

If you want to delete everything within the directory recursively, but not the directory itself, use:

```
\Koldy\Filesystem\Directory::emptyDirectory('/full/path/to/dir');
```

In both cases, if anything goes wrong, framework will throw `\Koldy\Filsystem\Exception`.