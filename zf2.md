# Zend Framework 5 Integration

To integrate __Filepicker__ with [Zend Framework 2](http://framework.zend.com/) follow these steps:

Copy the `assets` folder to your ZF2 `public` directory and the `uploader` folder into the root directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

```php
"Hazzard\\Filepicker\\": "uploader/src"
```

It should look like [this](http://i.imgur.com/Z7RQIvm.png).

Then run:

```bash
composer require hazzard/config
composer require intervention/image
composer require symfony/http-foundation ~2.7
composer dumpautoload
```

Create a `FilepickerController` controller and add:


```php
<?php

namespace Application\Controller;

use Hazzard\Filepicker\Uploader;
use Hazzard\Filepicker\Http\Event;
use Hazzard\Filepicker\Http\Handler;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;
use Symfony\Component\HttpFoundation\Request;

use Zend\Mvc\Controller\AbstractActionController;

class FilepickerController extends AbstractActionController
{
    public function indexAction()
    {
        $basepath = $this->getServiceLocator()
                    ->get('ViewHelperManager')->get('basepath');

        $handler = new Handler(
            new Uploader($config = new Config, new ImageManager)
        );

        $config['debug'] = true;

        // Path to the files directory (public/files).
        $config['upload_dir'] = 'public/files';

        // Url to the files directory.
        $config['upload_url'] = $basepath('files');

        $handler->handle(Request::createFromGlobals())->send();

        return $this->getResponse();
    }
}
```

In your `module.config.php` file, add to the `controllers > invokables` array:

```php
'Application\Controller\Filepicker' => Controller\FilepickerController::class,
```

And under the `routes` array add:

```php
'filepicker' => array(
    'type' => 'Zend\Mvc\Router\Http\Literal',
    'options' => array(
        'route'   => '/filepicker',
        'defaults' => array(
            'controller' => 'Application\Controller\Filepicker',
            'action'     => 'index',
        ),
    ),
),
```

> Notice: Make sure you use your proper namespace for the controller.


Then in your view make sure to set the [url](configjs.md#url) option to your controller route, like this:

```javascript
url: '<?php echo $this->url("filepicker") ?>',
```

If you are using CSRF validation, make sure to set the token using the [formData](configjs.md#formdata) option:

```javascript
formData: {
    csrf: '<?php echo $csrf->getValue(); ?>', // $csrf -> Zend\Form\Element\Csrf
}
```
