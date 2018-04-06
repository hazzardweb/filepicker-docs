# How-to

- [Rename Files](#rename-files)
- [Protect Files](#protect-files)
- [Fetch Files](#fetch-files)
- [Filter Files](#filter-files)
- [Send Data to Server](#send-data-to-server)
- [Insert Watermark](#insert-watermark)
- [Save to Database](#save-to-database)

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

## Insert Watermark

To insert a watermark, in the [after](configphp#after) option callback use the [insert](http://image.intervention.io/api/insert) method on the `$image` object (and don't forget to save it):

```php
/**
 * Insert watermark.
 *
 * @param  \Intervention\Image\Image $image
 * @param  string $filename
 * @param  string $version
 */
$watermark = function ($image, $filename, $version) {
    $image->insert(__DIR__.'/watermark.jpg', 'bottom-left')->save();
};

// Add watermark to the default image version.
$config['image_versions..after'] = $watermark;

// Add watermark to the "thumb" image version.
$config['image_versions.thumb.after'] = $watermark;
```

If you want to position the watermark somewhere else make sure to read the docs for the [insert](http://image.intervention.io/api/insert) method. 

## Save to Database

This example will show how to save files for a specified user. This could also be a page id or some other identifier for which you want to attach files to.

You will need a table for the files, a library to interact with the database and the id of the current user (for this example). 

For the database table let's let's use MySQL:

```mysql
CREATE TABLE `files` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `file_name` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `mime_type` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `size` int(10) unsigned NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```
The `user_id` you could use something else, like a `post_id`, `customer_id` etc.

Next we'll create a simple [PDO](http://php.net/manual/en/book.pdo.php) connection, but you could use your own database library:

```php
// uploader/index.php

$host = 'localhost';
$username = 'root';
$password = 'secret';
$dbname = 'filepicker';

$db = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

To save the files to database, use the [upload.success](apiphp.md#upload.success) event. 
Here you'll need the user id that you can either send it via [data](configjs.md#data) option or the session.

```php
$handler->on('upload.success', function ($file) use ($db) {
    // Here you may use $_POST['user_id'], $_SESSION['user_id'] etc. to ge the id of the user.
    $userId = 1;

    $db->prepare('INSERT INTO `files` (`user_id`, `file_name`, `mime_type`, `size`) VALUES (?, ?, ?, ?)')
        ->execute([$userId, $file->getFilename(), $file->getMimeType(), $file->getSize()]);
    
    // Set the id to be available on the client side via file.data.id
    $file->data = ['id' => $db->lastInsertId()];
});     
```

To only fetch files for a specified user use the [files.fetch](apiphp.md#files.fetch) event:

```php
$handler->on('files.fetch', function (&$files) use ($db) {
    $userId = 1;
    $limit = (int) $_GET['limit'];
    $offset = (int) $_GET['offset'];

    // Fetch files
    $stmt = $db->prepare("SELECT * FROM `files` WHERE `user_id` = ? LIMIT $limit OFFSET $offset");
    $stmt->execute([$userId]);
    $data = $stmt->fetchAll(PDO::FETCH_CLASS);

    $files = array_map(function ($file) { return $file->file_name; }, $data);

    // Fetch the total number of files
    $stmt = $db->prepare('SELECT count(id) as aggregate FROM `files` WHERE `user_id` = ?');
    $stmt->execute([$userId]);
    $data = $stmt->fetchAll(PDO::FETCH_CLASS);

    $total = isset($data[0]->aggregate) ? $data[0]->aggregate : 0;
});
```

To delete a file from database use the  [file.delete](apiphp.md#file.delete) event:

```php
$handler->on('file.delete', function ($file) use ($db) {
    $userId = 1;

    $stmt = $db->prepare('DELETE FROM `files` WHERE `user_id` = ? AND `file_name` = ?');
    $stmt->execute([$userId, $file->getFilename()]);
});
```
