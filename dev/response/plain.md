# Plain Text Response

You'll know when to return plain text response. This class will set `Content-Type` to `text/plain`.

Example of plain response:

```php
use Koldy\Response\Plain;

class IndexController
{
	public function robotsTxtAction()
	{
		return Plain::create("User-agent: *\nDisallow: /");
	}
}
```


[&larr; Back To Response](../response.md#framework-response-classes)