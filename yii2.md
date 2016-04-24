# Yii 2 Integration

To integrate __Filepicker__ with [Yii 2](http://www.yiiframework.com/) follow these steps:

Copy the `assets` folder to your Yii `web` directory and the `uploader` folder into the root directory.

Edit your `composer.json` file and add the following line to the `psr-4` autoload:

```php
"Hazzard\\Filepicker\\": "app/uploader/src"
```

It should look like [this](http://i.imgur.com/svFxR3z.png).

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

namespace app\controllers;

use Yii;
use yii\web\Controller;

use Hazzard\Filepicker\Handler;
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

class FilepickerController extends Controller
{
    public function actionHandle()
    {
        $handler = new Handler(
            new Uploader($config = new Config, new ImageManager)
        );

        $config['debug'] = true;

        // Path to the files directory (web/files).
        $config['upload_dir'] = Yii::getAlias('@webroot') . '/files';
        
        // Url to the files directory.
        $config['upload_url'] = Yii::getAlias('@web') . '/files';

        $handler->handle()->send();
    }
}

```

Then in your view make sure to set the [url](configjs.md#url) option to your controller route, like this:

```javascript
url: '<?php echo yii\helpers\Url::to(["filepicker/handle"]); ?>',
```

Add add the CSRF token using the [data](configjs.md#data) option:

```javascript
data: {
	_csrf: '<?php echo Yii::$app->request->getCsrfToken(); ?>',
}
```
