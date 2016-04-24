# CakePHP 3 Integration

Copy the `assets` folder to the `webroot` directory and the `uploader` folder into the `plugins` directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

```php
"Hazzard\\Filepicker\\": "plugins/uploader/src"
```

Then run:

```bash
composer require hazzard/config
composer require intervention/image
composer require symfony/http-foundation ^2.7
composer dumpautoload
```

Create a `FilepickerController` controller and add:

```php
<?php

namespace App\Controller;

use Cake\Routing\Router;
use Cake\Core\Configure;
use Cake\Controller\Controller;

use Hazzard\Filepicker\Handler;
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

class FilepickerController extends Controller
{
    public function initialize()
    {
        parent::initialize();
    }

    public function index()
    {
        $handler = new Handler(
            new Uploader($config = new Config, new ImageManager)
        );

        $config['upload_dir'] = WWW_ROOT.'files';
        $config['upload_url'] = Router::url('/files', true);
        $config['debug'] = Configure::read('debug');

        $handler->handle()->send();

        return $this->response;
    }
}
```

Edit your `routes.php` file and add: 

```php
Router::connect('/filepicker', ['controller' => 'Filepicker']);
```

Then in your view make sure to set the [url](configjs.md#url) option to `/filepicker` (or whatever route you choose).
