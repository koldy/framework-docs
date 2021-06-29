# JSON Response

If you're creating some restful API or just responding to Ajax request, this is the response you'll most likely use.

Json response will set the `Content-Type` header to `application/json`. It's using [Data helper](../data.md) to store
key-value data internally and everything you set, it'll be returned to client.

> DO NOT mix JSON response with [Json helper for encoding and decoding](../json.md).

Example of JSON response:

```php
use Koldy\Response\Json;

class UserController
{
	public function signInAjax()
	{
		return Json::create([
			'success' => true,
			'user_data' => [
				'first_name' => 'John',
				'last_name' => 'Doe',
				'age' => 30
			]
		]);
	}
}
```


[&larr; Back To Response](../response.md#framework-response-classes)