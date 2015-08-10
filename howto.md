# HowTo

- [Protect Files](#protect-files)
- [Sort Files](#sort-files)
- [Send Data to Server](#send-data-to-server)

## Protect Files

If you use Filepicker in a application with users you may want to protect the _files_ directory from users that are not logged in.

First create a `.htaccess` file inside the _files_ directory containing:

```php
Deny from all 
```

Next edit `uploader/index.php` and enable the [php_download](configphp.md#php_download) option: 

```php
$config['php_download'] = true;
```

Now in before any other event listeners register one with these events: [file.get](handler.md#fileget), [file.download](handler.md#filedownload), [file.delete](handler.md#filedelete) and [crop.before](handler.md#cropbefore):

```php
$handler->on(array('file.get', 'file.download', 'file.delete', 'crop.before'), function(Event $e) {
	// If the user is Unauthorized, then:
	$e->abort('Unauthorized');
	$e->preventDefault(); // Prevent other events to be fired.
});
```

To check if the user is authorized you may include a library from your app, call a function, check if a specific session is set, etc.

_Learn more about the [abort](handler.md#abort) and [preventDefault](handler.md#preventdefault) events._

## Sort Files

To sort the files you can simply register a listener for the [files.get](handler#filesget) event and add use [usort](https://php.net/manual/en/function.usort.php) for example to sort the files.

### Sort by filemtime 

```php
$handler->on('files.get', function(array &$files) {
	usort($files, function($a, $b) {
		if ($a->getMTime() === $b->getMTime()) {
			return 0;
		}
		
		// ">" - newest | "<" - oldest
		return ($a->getMTime() > $b->getMTime()) ? -1 : 1;
	});
});
```

### Sort by filesize 
```php
$handler->on('files.get', function(array &$files) {
	usort($files, function($a, $b) {
		if ($a->getSize () === $b->getSize ()) {
			return 0;
		}
		
		// ">" - descending | "<" - ascending
		return ($a->getSize () > $b->getSize ()) ? -1 : 1;
	});
});
```

### Sort by filename

For alphabetically order see the [sorting_order](configphp.md#sorting_order) option.

## Send Data to Server

If you wish to send data to the server you can do that by using the [formData](configjs.md#formdata) option. You can either pass an object of parameters or a function that returns an object.

Example 1:

```javascript	
$('#filepicker').filePicker({
	// ... 
	formData: {
		paramA: 'value1',
		paramB: 'value2',
	},
});
```

Example 2:

```javascript   
$('#filepicker').filePicker({
	// ... 
	formData: function() { 
		// Grab the value from an input.
		var val = $('#myinput').value();
		// Return the object.
		return {
			paramA: 'value 1',
			paramB: val
		} 
	},
});
```

On the server side in any of the handler [events](handler.md) you can access the data with `$_POST` (or `$_GET` when loading the files):

```php
$handler->on('upload.before', function(Event $e) {
	$valueA = $_POST['paramA'];
	$valueB = $_POST['paramB'];
});

$handler->on('file.get', function(Event $e) {
	$valueA = $_GET['paramA'];
	$valueB = $_GET['paramB'];
});
```
