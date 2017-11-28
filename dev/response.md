# Response

## Introduction

In MVC architecture, when controller is finished with its work, you have to return `Response`. Framework will accept
anything, from primitive values to printable object instances, but we recommend that you return any object instance which
extends `\Koldy\Response\AbstractResponse`. Framework comes with few predefined `Response` classes and all classes
are within `\Koldy\Response` namespace.


## Framework Response Classes

1. [View](response/view.md) - This is Koldy's *templating* engine. Use this when you need to print HTML or anything else.
2. [Plain](response/plain.md) - Use it for plain text. Good for robots.txt.
3. [Redirect](response/redirect.md) - Use this when you want to return `301` or `302` response.
4. [Json](response/json.md) - Use this one when you want to return JSON response. Good for APIs.
5. [FileDownload](response/file-download.md) - Good when you want to start file download on client's browser.


## Custom Response Classes

To create custom response class yourself, simply extend any of the framework's existing response classes or extend
`\Koldy\Response\AbstractResponse`.


## Benefits Of AbstractResponse

When you're using `AbstractResponse`, you'll notice that these classes automatically have methods for headers, status
code and before/after *work* manipulation. Headers and status codes are standard things when working with HTTP
requests, while *before* and *after* is function (or more than one function) that will be executed before/after
response was dumped to output buffer.

### before

Pass any number of functions to `before()` and all functions will be executed right before everything is sent to
output buffer. Functions will be executed in the same order as it was added to *queue*. This can be good chance to
cleanup unnecessary stuff or prepare output. Instance of the same `AbstractResponse` class is passed as first
parameter to the function you passed to `before()`.

```php
return View::create('something')
	->before(function (View $view) {
		Log::debug('Do something with View');
		$view->set('something', 'additional');
	});
```

### after

Pass any number of functions to `after()` and all functions will be executed right after everything is sent to
output buffer. Functions will be executed in the same order as it was added to *queue*. This can be good chance to
cleanup unnecessary stuff or to perform any time-consuming task that doesn't require client to know about, like,
sending an email in the background, or sending message to Slack.

When *after* functions are defined, framework will prepare *Content-Length* header so the client will close the
connection after it receives given number of bytes. That's why you're able to perform any time-consuming task *after*.

```php
return Json::create()
	->set('success', true)
	->after(function () {
		\YourApp\Slack::notification('Someone signed up!');
	});
```