# Redirect Response

Use this response class when you want to return `301` or `302` status codes with `Location` header, meaning you
want to redirect client to another location.

Examples:

```php
use Koldy\Response\Redirect;

class UserController
{
	public function signInAction()
	{
		return Redirect::temporary('/other-location');
		// OR:
		// return Redirect::to('/other-location');
	}
	
	public function signIn2Action()
	{
		return Redirect::permanent('/other-location');
	}
	
	public function signIn2AssetAction()
	{
		return Redirect::asset('images/logo.png');
	}
	
	public function homeAction()
	{
		return Redirect::home();
	}
	
	public function hrefAction()
	{
		return Redirect::href('controller', 'action', ['page' => 2]);
	}
}
```


[&larr; Back To Response](../response.md#framework-response-classes)