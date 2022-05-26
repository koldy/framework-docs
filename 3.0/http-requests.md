# HTTP Requests

## Introduction

This page is about HTTP requests that are going from your application to some remote server.

To perform such HTTP request, this framework is using cURL so you need to have `php8.1-curl` extension installed on your
system.

All PHP classes are within the `\Koldy\Http` namespace, so in order for example to work on this page, you have to:

```php
use Koldy\Http;
```

## Quick Request

There are few methods prepared for you to make *quick* HTTP requests:

```php
$response = Request::get('https://koldy.net');
```

`$response` will be instance of `\Koldy\Http\Response` which has all the information you need, like:

```php
echo $response->getBody();
echo $response->getHttpCode(); // 200 if it's OK
echo $response->getTotalTimeMs(); // get total execution time in milliseconds
```

To set additional parameters and/or request headers to *quick requests*, simply append it as:

```php
$params = ['page' => 2];
$headers = ['Content-Type', 'application/json'];
$response = Request::get('https://koldy.net', $params, $headers);
```

Beside `Request::get()`, you can use `Request::post()`, `Request::put()` and `Request::delete()`.


## Constructing Request With More Options

To construct Request object with more options, simply initialize new instance:

```php
$request = new Request();
$request->setUrl('https://koldy.net');
$request->setMethod(Request::POST);
$request->setParam('page', 2);
$request->setHeader('auth', md5('secret'));
$request->setOption(CURLOPT_FOLLOWLOCATION, true);
$request->setOption(CURLOPT_MAXREDIRS, 5);

Log::debug($request);
$response = $request->exec();
Log::debug($response);
```

In the example above, you can see how to define request with all optional settings. When using `setOption()` method,
feel free to use any [CURL_*](http://php.net/manual/en/function.curl-setopt.php) PHP constant with corresponding
value.


## Troubleshooting

When troubleshooting, you can call `$request->debug()` or `$response->debug()` or simply print these variables and you'll
get some useful information about the performed actions.  
