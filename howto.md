# How-to

- [Rename Files](#rename-files)
- [Protect Files](#protect-files)
- [Fetch Files](#fetch-files)
- [Filter Files](#filter-files)
- [Send Data to Server](#send-data-to-server)

## Rename Files

To rename an uploaded file use the [upload.before](apiphp.md#uploadbefore) event:

```php
$handler->on('upload.before', function ($file) {
    $file->save = 'myfile';
    // OR
    $file->saveWithExtension = 'myfile.txt';
});
```

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

Now in before any other event listeners register one with these events: [upload.before](apiphp.md#uploadbefore), [files.fetch](apiphp.md#filesfetch), [file.download](apiphp.md#filedownload), [file.delete](apiphp.md#filedelete), [crop.before](apiphp.md#cropbefore):

```php
$handler->on(array('upload.before', 'files.fetch', 'file.download', 'file.delete', 'crop.before'), 
    function () {
	// If the user is Unauthorized, then:
	throw new \Hazzard\Filepicker\Exception\AbortException('Unauthorized');
});
```

To check if the user is authorized you may include a library from your app, call a function, check if a specific session is set, etc.

## Fetch Files

By default all files will be returned, but you may want to return just some files, for example the ones that belong to a specific user. To do that use the [files.fetch](apiphp.md#filesfetch) event.

```php
$handler->on('files.fetch', function (&$files) {
    // Here you may fetch just some files from the database based on the current user, etc.

    // $files = array('file1.txt', 'file2.jpg', 'file3.zip');
});
```

## Filter Files

Instead of returning the files you want to show, you can use the [files.filter](apiphp.md#filesfilter) event to filter the files.

The `$files` is an array of [file](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) instances and `$total` is the number of files. You may iterate through the files and unset some.

```php
$handler->on('files.filter', function (&$files, &$total) {
    foreach ($files as $index => $file) {
        if ($index % 2 == 0) { 
            unset($files[$index]);
            $total--;
        }
    }
});
```

## Send Data to Server

To send data to the server use the [data](configjs.md#data) option. You can either pass an object or a function that returns an object.

```javascript   
// Example 1

$('#filepicker').filePicker({
	// ... 
	data: {
		paramA: 'value1',
		paramB: 'value2',
	},
});

// Example 2

$('#filepicker').filePicker({
	// ... 
	data: function() { 
		// Grab the value from an input.
		var val = $('#myinput').val();
		
        // Return the object.
		return {
			paramA: 'value 1',
			paramB: val,
		} 
	},
});
```

On the server side in any of the handler [events](apiphp.md#available-events) you can access the data with from the request:

```php
$handler->on('upload.before', function ($file) use ($handler) {
	$valueA = $handler->request()->get('paramA'); // <=> $_POST['paramA']
	$valueB = $handler->request()->get('paramB'); // <=> $_POST['paramB']
});

$handler->on('files.fetch', function (&$files) use ($handler) {
	$valueA = $handler->request()->get('paramA'); // <=> $_GET['paramA']
	$valueB = $handler->request()->get('paramB'); // <=> $_GET['paramB']
});
```

Make sure you add `use ($handler)` to use the `$handler` variable inside the scope of the function.
