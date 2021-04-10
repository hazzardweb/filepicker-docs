# Laravel 5 Integration

To integrate __Filepicker__ with [Laravel](https://laravel.com) follow these steps:

Copy the `assets` folder to your Laravel `public` directory and the `uploader` folder into your `app` directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

```php
"Hazzard\\Filepicker\\": "app/uploader/src"
```

Then run:

```bash
composer require hazzard/config
composer require intervention/image
composer dumpautoload
```

Create a `FilepickerController` controller and add:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

use Hazzard\Filepicker\Handler;
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

class FilepickerController extends Controller
{
	/**
	 * @var \Hazzard\Filepicker\Handler
	 */
	protected $handler;

	/**
	 * Create a new controller instance.
	 */
	public function __construct()
	{
		$this->handler = new Handler(
			new Uploader($config = new Config, new ImageManager)
		);

		$config['upload_dir'] =  public_path('files');
		$config['upload_url'] = '/files';
		$config['debug'] = config('app.debug');
	}

	/**
	 * Handle an incoming HTTP request.
	 *
	 * @param  \Illuminate\Http\Request $request
	 * @return \Symfony\Component\HttpFoundation\Response
	 */
	public function handle(Request $request)
	{
		return $this->handler->handle($request);
	}
}
```

Edit your `routes.php` file and add: 

```php
Route::any('filepicker', 'FilepickerController@handle');
```

Then in your view make sure to set the [url](configjs.md#url) option to `/filepicker` (or whatever route you choose) and add the [CSRF Token](http://laravel.com/docs/master/routing#csrf-protection) in the [data](configjs.md#data) array:

```javascript
data: {
	_token: "{{ csrf_token() }}"
}
```

#### Using with Laravel Mix

First copy all the `filepicker*.js` files from `assets/js` to your project folder in `resources/assets/js/vendor`.

Then add some aliases to your `webpack.mix.js` file:

```javascript
const path = require('path')

mix.webpackConfig({
    resolve: {
        alias: {
            'filepicker': path.join(__dirname, './resources/assets/js/vendor/filepicker'),
            'filepicker-ui': path.join(__dirname, './resources/assets/js/vendor/filepicker-ui'),
            'filepicker-drop': path.join(__dirname, './resources/assets/js/vendor/filepicker-drop'),
            'filepicker-crop': path.join(__dirname, './resources/assets/js/vendor/filepicker-crop'),
            'filepicker-camera': path.join(__dirname, './resources/assets/js/vendor/filepicker-camera'),
        }
    }
})
```

Now you can import the Filepicker files like this:

```javascript
import 'filepicker'
import 'filepicker-ui'
import 'filepicker-drop'
import 'filepicker-crop'
import 'filepicker-camera'
```

