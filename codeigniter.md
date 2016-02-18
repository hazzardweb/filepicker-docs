# CodeIgniter 3 Integration

## Prerequisites

- You have [Composer](https://getcomposer.org/) installed on your machine.

- Composer autoload is enabled. In `application/config/config.php` make sure `$config['composer_autoload']` is set to `FCPATH.'vendor/autoload.php'` (or whatever path you have composer installed in).

- The `url` helper is loaded in `application/config/autoload.php` (`$autoload['helper'] = array('url');`).

- You have set `$config[base_url']` in `application/config/config.php`.

## Installation

Copy the `assets` folder to your CodeIgniter root directory and the `uploader` folder into your `application/third_party` directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

```php
"Hazzard\\Filepicker\\": "application/third_party/uploader/src"
```

It should look like [this](http://i.imgur.com/TTq1wLn.png).

Then run:

```bash
composer require hazzard/config
composer require intervention/image 2.2.*
composer require symfony/http-foundation 2.5.*
composer dumpautoload
```

Create a `Filepicker` controller and add:

```php
<?php

use Hazzard\Filepicker\Uploader;
use Hazzard\Filepicker\Http\Event;
use Hazzard\Filepicker\Http\Handler;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;
use Symfony\Component\HttpFoundation\Request;

class Filepicker extends CI_Controller
{
    /**
     * Handle an incoming HTTP request.
     */
    public function index()
    {
        $handler = new Handler(
            new Uploader($config = new Config, new ImageManager)
        );

        $config['debug'] = true;

        // Path to the files directory (files).
        $config['upload_dir'] =  FCPATH . 'files';

        // Url to the files directory.
        $config['upload_url'] = base_url('/files');
    

        // Listen for events.
        // http://docs.hazzardweb.com/filepicker/1.0/handler#events
        // $handler->on('upload.before', function(Event $e) {
        // });
        
        // Handle an incoming HTTP request and send the response.
        $handler->handle(Request::createFromGlobals())->send();
    }
}
```

In your view make sure to set the [url](configjs.md#url) option to `"<?php echo site_url('filepicker') ?>"` (or whatever route you choose).
