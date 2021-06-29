# Database Migrations

Migrations are useful if you're working in a team and you want to *distribute* your changes on database to your team or
when you want to automate database changes using any continuous integration (CI) system.

Creating, migrating and rolling back scripts are generating log messages with useful information, so if anything goes
wrong, you should check your log for more details. If you don't see any messages when running scripts, please
[configure log in CLI environment](../configuration.md#log) for [debug, notice and info messages](../log.md#levels).


## Creating Migration

To create migration, in your project root, run:

```
./koldy create-migration my-first-migration
```

You should see the output similar to this:

```
Started CLI script: koldy
Created DB migration with name: my-first-migration
You may now open application/migrations/1505861577_MyFirstMigration.php and add your migration code
Done with CLI script: koldy
```

Now open the generated file. In our case, it's `1505861577_MyFirstMigration.php` which looks like this:

```
<?php declare(strict_types = 1);

use Koldy\Db;
use Koldy\Db\Migration;

/**
 * Migration file for Migration_1505861577_MyFirstMigration. Take your time to write down() method!
 *
 * Migrations are not executed as SQL transactions, so if you put multiple SQL statements in up() method, be aware that
 * any statement can fail at any time, which is making down() method hard to write.
 *
 * Generated at Tue, 19 Sep 2017 22:52:57 +0000
 */
class Migration_1505861577_MyFirstMigration extends Migration
{

    /**
     * Will be executed when migrating "up"
     */
    public function up(): void
    {
        //Db::query('ALTER TABLE users ADD is_active tinyint(1) NOT NULL DEFAULT 1')->exec();
    }

    /**
     * Will be executed when rolling back "down"
     *
     * Remember: down() is easier to write if up() doesn't contain a lot of SQL statements.
     */
    public function down(): void
    {
        //Db::query('ALTER TABLE users DROP COLUMN is_active')->exec();
    }

}
```

Under `up` method, put any query you want to execute when migrations are started.

Under `down` method, put the steps that should be performed when rolling back so after running `down` method, system
should come back to the same state as it was before running `up` from this migration.

> We highly recommend to write `down` steps. Trust us, this is not wasting time. If something goes wrong and you don't
have `down` steps, you'll probably spend much more time rolling back than time to write `down` steps.

As you can see in example above, we've already prepared and commented out `Db::query()->exec()` statements for raw
queries. As seen in [basics](basics.md#running-raw-sql-queries), we don't recommend writing raw query
statements for [select](query-builders.md#select), [insert](query-builders.md#insert),
[update](query-builders.md#update) and [delete](query-builders.md#delete), but we do recommend
writing raw queries for all other statements, like `alter table`, `create table` and such.

Although we're working with similar relational databases, each database has its own data types and differences and
it's almost impossible to cover all cases with simplified query builders. This is why there's no query builders for
`alters`, `creates`, `drops` and other statements in Koldy Framework and it's not scheduled to develop either. This
has to be simple job and the simplest is just to write raw query.

If you plan to use [insert](query-builders.md#insert), [update](query-builders.md#update) or
[delete](query-builders.md#delete) in migrations, then you're encouraged to use
[query builders](query-builders.md) or [database models](models.md) to perform these operations.


## Running Migrations

To run all migrations you have in queue, simply run:

```
./koldy migrate
```

> If you're using Vagrant, then you should execute this command inside Vagrant machine.

And:

> Beware that this is dangerous command and you should be careful when working with huge data sets or on databases
with high load.

When migration is generated, name gets prefixed with the timestamp which helps us determine in which order should we
execute migrations. Latest migrations might rely on alters made by previous migrations so order does matter a lot.

When starting migrations, framework will check the state of previously executed migrations by looking in
`koldy_migration` table in database and it'll compare with the file system. If there's new migration which has
timestamp older than the latest executed migration, then framework won't execute that *new* migration for security
reasons and the reason that **order is importatnt**.


## Forcing Migrations To Run

Now that you know that order matters a lot, it is possible that you might get into situation where you'll get "older",
but not executed migrations into your project. This case usually happens in larger teams when one team member is working
in different branch where new migrations are created and later merged to master branch. Or you have some **dev** branch
way ahead your production build and you had to push migration as patch on production server. All these cases will lead
to the case with "older" migrations in your project which were created before the last executed migration.

To solve this, use `--force` flag when running migrations from CLI:

```
./koldy migrate --force
```

> REMEMBER that this is dangerous command and you should know exactly what framework will do.

When passing `--force` to migrate script, framework will execute all migrations from filesystem that were not executed
before. It'll respect the creation order, so even if you have several migration files "from before", running them should
not produce any error.


## Rolling Back Migrations

To roll back migration(s), simply run:

```
./koldy rollback
```

> If you're using Vagrant, then you should execute this command inside Vagrant machine.

This command will roll back one migration only. You can run this command as many times as you want, until you run out of previously executed migrations. To run more than one roll back at once, set the number of steps as next argument:

```
./koldy rollback 5
```

This example will roll back last 5 migrations.


## Database Configuration For Migrations

When working with multiple database connections, you should decide where should framework write information about executed migrations. If you do nothing, framework will use first database connection by default and in some cases, this might not be wanted behaviour.

To set where should framework write this information, simply define `koldy_migration` key in `configs/database.php`. For example, if you have configuration [like this example](../database.md#configuration) and you want to write migrations info to `admin` database, you should append this to `configs/database.php`:

```
'koldy_migration' => 'admin'
```
