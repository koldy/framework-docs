# CLI

`CLI` stands for `Command Line Interface`. These are scripts executed on server directly by PHP interpreter without the need of having web server.

Koldy framework offers easy way of writing CLI scripts.

To make your own script for CLI env, create php file in `application/scripts` or under `application/modules/[module]/scripts`. The file name is allowed to have only English lower and upper case letters, numbers, dashes (-) and underscores (_). All other characters are not valid and framework will throw an exception if you try to use something else.

When you create new script, just start writing your code. There is nothing else you need. You can use anything you want in your scripts and everything in your project is accessible.

If you run script that are located in `application/scripts` and you need to use some class in module, then you'll have to manually register module with `Application::registerModule()` method so you can actually include the classes from there.


## Running CLI scripts

To run the script, you need to execute `./koldy run script-name` in your project's root where `script-name` is the file name you want to run.

Example: if you have script located on `application/scripts/backup-news.php`, then you need to run:

```
./koldy run backup-news
```

If you're running your script as CRON job, then you'll probably need to define something like this in your CRON rules:

```
/path/to/your-project/koldy run backup-news
```

## Running CLI scripts in modules

If you want to run the script in module, then call your script with this pattern:

```
./koldy run module-name:script-name
```

Example: If you have module named "news" and "backup-news" script it located under the "news" module on `application/modules/news/scripts/backup-news.php`, then you can execute that script with:

```
./koldy run news:backup-news
```


## CLI parameter(s)

If you need to pass additional parameter to your script, then feel free to add anything you need after the script name.

There is `\Koldy\Cli` class in framework that can help you easily fetch the parameters you passed to the script.

Example: if you run your script as this:

```
./koldy run backup-news --my-parameter="this is cool"
```

Then you can fetch the value of your parameter with:

```
Cli::getParameter('my-parameter'); // it will return "this is cool"
```

## Working with PHP versions

The `koldy` script is located in your project's root and by default, it calls `php` interpreter. If you're running multiple PHP versions on the same server, then you should probably want to change
the interpreter call in `koldy` shell script. So, if you want to run your CLI scripts with PHP 8.1, then use `php8.1` for PHP 8.1, `php7.4` for 7.4 and etc.
