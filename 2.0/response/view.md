# Views

## Introduction

This is the *response* type you'll use for the most of your static (HTML) content. We could say that `View` response
is actually *template* and you might be right because you can pass variables and render other *views* inside view,
but there's no special templating engine nor it'll ever be. We think PHP itself is powerful enough so no extra
syntax is needed. And that's why framework's error handler will work out of the box in templates as well.

Every `View` class needs its `.phtml` pair which will be used when flushing content to output buffer, so let's call
that `.phtml` file as **template** and let's call `View` class as **view**.


## Configuration

You need to say which folder is the root for all the templates you'll be using. If it's not set, framework will use
`[applicationPath]/views` folder by default.

All template files must have `.phtml` extension.


## Responding With View

As seen in other response classes, you can return `View` response from your controller's method:

```php
use Koldy\Response\View;

class IndexController
{
	public function indexAction()
	{
		return View::create('base');
	}
}
```

When creating view, you must tell which template file you'll be using. In the example above, `views/base.phtml` will be used.
Template file(s) can contain anything you want and you can embed PHP within the templates just like in old days. We
encourage the following syntax in templates:

```php
<h1>Hi <?= $this->variable_name ?></h1>

<h2>Conditions</h2>
<?php if ($this->has('variable_name')) : ?>
	We have the variable set!
<?php endif; ?>

<h2>Loops</h2>
<ul>
<?php foreach ($this->get('some_array') as $key => $value) : ?>
	<li>We have <?= $key ?> and <?= $value ?></li>
<?php endforeach; ?>
</ul>



```


## Passing Variables To Templates

`View` class has [Data helper](../data.md) so you can easily set key-value pairs which could be used in template files.

```php
return View::create('base')
	->set('first_name', 'John');
```

You can print `first_name` name in template like this:

```html
<h1>Hi <?= $this->first_name ?></h1>
```

Or:

```html
<h1>Hi <?= $this->get('first_name') ?></h1>
```

## Rendering Another View

To render another template inside of template, use:

```php
<div>
	<?= $this->render('another') ?>
</div>
```

Framework will look up for `views/another.phtml` and it'll print on the position of `render()` method. You may pass
set of variables as second parameter of `render()` method which is useful if you're printing some list of products and
each product has different values in it.

`ProductsController.php`:
```php
use Koldy\Response\View;

class ProductsController
{
	public function indexAction()
	{
		return View::create('products')
			->set('products', [
			[
				'id' => 1,
				'name' => 'First product',
				'description' => 'It\'s really cool!'
			],
			[
				'id' => 2,
				'name' => 'Second product',
				'description' => 'It\'s cool even more!'
			]
		]);
	}
}
```

`views/product.phtml`:
```php
<div class="product" data-id="<?= $id ?>">
	<h3><?= $name ?></h3>
	<p><?= $description ?></p>
</div>
```

`views/products.phtml`:
```php
<div class="products>
	<?php foreach ($this->products as $product) : ?>
		<?= $this->render('product', $product) ?>
	<?php endforeach; ?>
</div>
```

> NEVER DO any logic inside templates! Template should only print data and eventually loop through the prepared data and
nothing else! All logic should be inside controller or inside your class which extends View class.



[&larr; Back To Response](../response.md#framework-response-classes)