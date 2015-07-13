# Laravel 5 Integration

To integrate __Filepicker__ with [Laravel 5](http://laravel.com/) follow these steps:

Copy the `assets` folder to your Laravel `public` directory and the `uploader` folder into your `app` directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

	"Hazzard\\Filepicker\\": "app/uploader/src"

Then run:

	composer require hazzard/config
	composer require intervention/image
	composer dumpautoload

Create a `FilepickerController` controller and add:

	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller as BaseController;

	use Hazzard\Filepicker\Uploader;
	use Hazzard\Filepicker\Http\Event;
	use Hazzard\Filepicker\Http\Handler;
	use Intervention\Image\ImageManager;
	use Hazzard\Config\Repository as Config;

	class FilepickerController extends BaseController
	{
		/**
		 * @var \Hazzard\Filepicker\Http\Handler
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

Edit your `routes.php` file and add: 

	Route::any('filepicker', 'FilepickerController@handle');

Then in your view make sure to set the [url](configjs.md#url) option to `/filepicker` (or whatever route you choose) and add the [CSRF Token](http://laravel.com/docs/master/routing#csrf-protection) in the [formData](configjs.md#formData) array:

	formData: {
		_token: "{{ csrf_token() }}"
	}
