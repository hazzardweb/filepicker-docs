# Filepicker Structure

```markup	
.
├── *.html
└── assets
|	├──	css
|	├── less
|	└── js
|	    ├── jquery.filepicker.js
|	    ├── plugins
|		└── vendor
├── files
├── vendor
├──	composer.json
└── uploader
    ├── *.php
	└── src
		├── Uploader.php
		└── Http
			├── Handler.php
			└── Event.php
```

The `*.html` files are the Front-end examples with HTML and JavaScript.

The `assets` directory, as the name implies, contains all of the assets files. 

The `assets/css` folder contains all of the css files and `assets/less` all of the [less](http://lesscss.org/) files.

The `assets/js` directory, as you might expect, contains the JavaScript files:

- The`jquery.filepicker.js` is the main JavaScript file of the uploader.
- The `plugins` folder contains additional plugins for uploader.
- The `vendor` contains the JavaScript dependencies.

The `files` folder contains the uploader files.

The `vendor` directory (`composer.json`) contains the [Composer](https://getcomposer.org) dependencies.


The `uploader` directory, contains the core code of the script.

The `uploader/*.php` files are the Back-end files, to wich the requests are sent to.

The `uploader/src` folder contains all of the Filepicker PHP classes.

- `Uploader.php` - The main uploader class.
- `Http/Handler.php` - The uploader HTTP handler.
- `Http/Event.php` - The upload event used by the handler.
