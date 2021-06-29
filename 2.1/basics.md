# Basics

Now that you know what [project structure](project-structure.md) looks like and how
[things work](architecture-concepts.md), you need to know how to [accept request](requests.md),
do something and then return some [response](response.md) back to client.

Koldy framework respects MVC architecture so overall picture would look like this:

1. [Request](requests.md) is passed from web server to `public/index.php`
2. Framework boots up respecting all [mandatory configuration](configuration.md#mandatory-index-php)
3. Framework initializes [routing class](routes.md) and finds out which controller and action should be used
4. Framework executes method in your controller and expects instance of any [response](response.md) back

[Next &rarr;](requests.md)