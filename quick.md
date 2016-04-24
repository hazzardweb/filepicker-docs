# Quickstart

This is just a basic example that includes the upload button and a list of uploaded files.

__CSS__

First include the CSS file inside the `<head>` tag. 

```markup
<link rel="stylesheet" href="assets/css/filepicker.css">
```

__HTML__

Next, add the HTML markup.

```markup
<div id="filepicker">
    <!-- The upload button -->
    <input type="file" name="files[]">
    
    <!-- The files list -->
    <ul class="files"></ul>
</div>
```

__JavaScript__

At the bottom of your page right before closing the `<body>` tag add:

```markup
<script src="https://code.jquery.com/jquery-1.12.3.min.js"></script>
<script src="assets/js/filepicker.js"></script>
```

```javascript
$('#filepicker').filePicker({
    url: 'uploader/index.php',
})
.on('done.filepicker', function (e, data) {
    var list = $('.files');

    // Iterate through the uploaded files
    $.each(data.files, function (i, file) {
        if (file.error) {
            list.append('<li>' + file.name + ' - ' + file.error + '</li>');
        } else {
            list.append('<li>' + file.name + '</li>');
        }
    });
})
.on('fail.filepicker', function () {
    alert('Oops! Something went wrong.');
});
```

> Note: If you already have jQuery in your page, you don't have to include it again.

__PHP__

In your PHP file add:

```php
<?php

use Hazzard\Filepicker\Handler;
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

// Include composer autoload
require __DIR__.'/../vendor/autoload.php';

$uploader = new Uploader($config = new Config, new ImageManager);
$handler = new Handler($uploader);

// Configuration
$config['debug'] = true;
$config['upload_dir'] = __DIR__.'/../files';
// $config['upload_url'] = 'files';
```

For more examples check the source code in these files: 

- Basic UI: `index.php` 
- Basic Plus UI: `plus.php`
- Basic: `basic.php`
- Avatar: `avatar.php`
- Zero UI: `zero.php`
- `uploader/index.php`
