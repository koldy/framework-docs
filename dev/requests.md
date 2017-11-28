# Requests

Every requests that comes into system gets processed in this order:

1. Request is passed from your web server to `public/index.php`
2. Framework get's initial [mandatory configuration](configuration.md#mandatory-index-php) and initializes all
mandatory stuff for the request cycle, such as paths, autoloader, error handler, shutdown stuff, log(s) and etc.
3. Then `\Koldy\Application::run()` is called which takes the previously prepared configurations and initializes
instance of the **route class** defined in [mandatory configuration](configuration.md#mandatory-index-php) under
`routing_class` - the **route class** then takes over the request and everything else is under routing class control.

> This doesn't apply on [CLI scripts](cli.md).

What happens next depends about [route and its options](routes.md), even [error handling](routes.md#error-handling).
You can find all available routes [here](routes.md#available-routes).