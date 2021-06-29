# Routes

## Introduction

When HTTP [request](request.md) gets into system, it needs to be process. Route tells the system
**how** the request should be processed. By default, Koldy framework ships [DefaultRoute](routes/default-route.md)
which should cover 99% of all cases you might run into.

Every route class should be able to:

1. Parse REQUEST_URI and know what is controller, what is action and what are parameters
2. Should know where to look up for [modules](modules.md), controllers and actions
3. Should execute action in controller and return [response](response.md) back to framework
4. Should respect if controller has `before()` or `after()` methods
5. Know what to do when action execution fails, where to look up exception handler and execute it

We encourage you to use our [DefaultRoute](routes/default-route.md) class, although you're not limited to
use our routing class exclusively. Routing class has the same abstraction architecture just like [logging](log.md#creating-custom-logger),
[caching](cache.md#creating-custom-cache-storage-adapter) or [mailing system](mail.md#creating-custom-adapter),
so it's possible to [create custom routing class](#custom-routing-class).


## Error Handling

If framework fails to execute your method, it'll throw `\Koldy\Response\Exception\ServerException`. If
controller or action (method) is not found, routing class should throw `\Koldy\Response\Exception\NotFoundException`.
Error handler customization depends on the [routing class](routes.md) you're using, so check the
documentation on the routing class you're using.


## Available Routing Classes

Currently, there's only one routing class available:

1. [DefaultRoute](routes/default-route.md)


## Custom Routing Class

To create custom routing class, create your own class anywhere within the include path, extend
`\Koldy\Route\AbstractRoute`, implement methods, set the new class as [routing_class](configuration.md#routing-class-and-routing-options)
and that's it.