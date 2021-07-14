# Getting Started

This documentation assumes that you have the knowledge of PHP syntax, basics about relational databases
and MVC. If you need to google any of the words in this block, then you're probably not on the right place.

## Requirements

The system itself has the following requirements:

* PHP >= 7.3
* OpenSSL PHP extension
* PDO PHP extension

If you're using Linux Ubuntu or Debian, you can use this:

```bash
apt-get install php7.4-fpm php7.4-cli php7.4-common php7.4-mysql php7.4-mbstring php7.4-pgsql php7.4-sqlite php7.4-gd php7.4-curl php7.4-bcmath php7.4-xml
```

## Development Environment

Setting up all technologies you need on your computer can take a lot of time. To speed up this process, we
recommend [Vagrant](https://www.vagrantup.com) virtual machine which makes the whole process much easier.

The basic boilerplate you can use is [boilerplate](https://github.com/koldy/boilerplate) which is project ready for you
to test or start your own project. To get it, run the following command in your terminal:

```
{{frameworkBoilerplateCloneCommand:2.2}} && cd boilerplate && ./init.sh
```

After it's done, open http://localhost:5000

Vagrant VM will install PHP 7.4, Postgres, MySQL and Nginx. It'll adjust configuration for development and it'll put web server
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
