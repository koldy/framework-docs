# File Download Response

When you want to initiate download on client's browser, you have two options: redirect to the file that will be served
by web server, of, if file is behind session and should be accessible to anyone, then you'll need to process file
download through backend.

Use `\Koldy\Response\FileDownload` to initiate file download with all headers already prepared for you.

Example of file download:

```php
use Koldy\Response\FileDownload;

class IndexController
{
	public function downloadFileAction()
	{
		return FileDownload::create('/full/local/path/to/file.pdf');
	}
	
	public function downloadFile2Action()
	{
		// download file under different name
		return FileDownload::create('/full/local/path/to/file.pdf', 'Report.pdf');
	}
	
	public function downloadFile3Action()
	{
		// download file with forced content-type header
		return FileDownload::create('/full/local/path/to/file.html', 'Report.html', 'text/plain');
	}
}
```

We recommend that you always use full path to local file so there's less chance you'll get confused in paths.

Framework ships a lot of content types as well, so if you need to manually choose content type using file extension,
you may use `\Koldy\Http\Mime` class to get it.

```php
use Koldy\Response\FileDownload;
use Koldy\Http\Mime;

class IndexController
{
	public function downloadFileAction()
	{
		return FileDownload::create('/tmp/app.ipa', 'MyApp.ipa', Mime::getMimeByExtension('ipa'));
	}
}
```



[&larr; Back To Response](../response.md#framework-response-classes)