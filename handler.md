# HTTP Handler

- [Introduction](#introduction)
- [Events](#events)
- [RESTful API](#restful-api)

## Introduction

The HTTP Handler class (`src/Http/Handler.php`) handles incoming HTTP requests and sends a response back to 
the client.

The handler constructor takes as parameter an instance of `src/Filepicker/Uploader.php` class. You can attach an listen for events and at the end handle the request and send a response.
	
	use Hazzard\Filepicker\Http\Event;
	use Hazzard\Filepicker\Http\Handler;
	use Symfony\Component\HttpFoundation\Request;

	// Create a new handler instance.
	$handler = new Handler($uploader);
	
	// Listen for events:
	$handler->on('upload.before', function(Event $e) {
		// Fired before the upload starts.
	});
	
	$handler->on('upload.success', function(Event $e) {
		// Fired for a successfully upload.
	});

	// Handle an incoming HTTP request and send the response.
	$handler->handle(Request::createFromGlobals())->send();

If you want to register multiple events with the same callback function you can pass an array of event types:

	$handler->on(array('file.get', 'file.download', 'file.delete', 'crop.before'), function(Event $e) {
		// If the user is Unauthorized, then:
		$e->abort('Unauthorized');
		$e->preventDefault(); // Prevent other events to be fired.
	});

## Events

The object `$e` is an instance of the `src/Filepicker/Http/Event.php` class and is a used to pass data to the callback functions, cancel uploads, attach data, etc.

The event `$e` has the following methods:

### getFile

Returns an instance of [UploadedFile](https://github.com/symfony/HttpFoundation/blob/2.6/File/UploadedFile.php) when the file is about to be uploaded (`upload.before` and `upload.error`) and [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) after the file is uploaded. Both classes extend the  [SplFileInfo](http://php.net/manual/en/class.splfileinfo.php) class.

	$file = $e->getFile();

To access the `$file` properties use:
	
- `$file->getFilename()` — Gets the filename
- `$file->getExtension()` — Gets the file extension
- `$file->getSize()` — Gets file size
- `$file->getMTime()` — Gets the last modified time
- `$file->getRealPath()` — Gets absolute path to file

_See [SplFileInfo](http://php.net/manual/en/class.splfileinfo.php) for all the available methods._

> Notice: In the `upload.before` and `upload.error` events use:
>  - `$file->getClientOriginalName()` instead of `$file->getFilename()`
>  - `$file->getClientOriginalExtension()` instead of `$file->getExtension()`
>  - `$file->getClientSize()` instead of `$file->getSize()`

### abort

Aborts the event from continuing executing.

	$e->abort('Custom error message!');

### preventDefault

Prevent events of the same type to be fired. If you have the same event register multiple times you may want to execute only the first and prevent the next one from executing.

	$e->preventDefault();

Example:

	$handler->on(array('file.get', 'file.download', 'file.delete', 'crop.before'), function(Event $e) {
		// If the user is Unauthorized, then:
		$e->abort('Unauthorized');
		$e->preventDefault(); // Prevent other events to be fired.
	});

	$handler->on('file.download'), function(Event $e) {
		// This event will not be fired.
	});

### setFilename

Changes the save name of the file. Can be used in the `upload.before` and `crop.before` events.

	$e->setFilename('custom_file_name');

If you wish to change the extension too, set the second parameter to `false`.

	$e->setFilename('custom_file_name.jpg', false);

### setData 

Attaches custom data to the file that will be available in the file response object.

	$e->setData(array('file_id' => 123));

### getImage

Returns an instance of [Image](https://github.com/Intervention/image/blob/master/src/Intervention/Image/Image.php), if the file is an image. This method can be only used in the `crop.before` and `crop.after` events.

	$image = $e->getImage();

If you need the image instance in other events use something like this (make sure the file is actually an image):

	$image = $handler->uploader->getImageManager()->make($e->getFile());

## Available Events

### upload.before

Event fired before the file upload starts. Use [abort](#abort) to cancel the file upload, [setFilename](#setfilename) to rename the file.

### upload.success

Event fired for a successfully upload. Use [abort](#abort) to cancel and delete the uploaded file.

### upload.fail

Event fired for a failed upload. Use `$e->getError()` to get the error message and `$e->setError('Custom error message')` to set a custom error message.

### files.get
Event fired on files access (When the files are loaded). The `$files` argument is an array of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) and you can remove/sort etc. See [Sort Files](howto.md#sort-files).

	$handler->on('files.get', function(array &$files, &$total) {
		
	});

### file.get

Event fired on file access. Use [abort](#abort) to exclude the file from the response.

### file.download

Event fired on file download. Use [abort](#abort) to cancel the download.

### file.delete

Event fired on file deletion. Use [abort](#abort) to cancel the deletion.

### crop.before

Event fired before the crop starts. 

### crop.after

Event fired on crop completion. Use [getImage](#getImage) to get the image instance.

## RESTful API

The RESTful API allows you to perform requests to the server to upload, update, retrieve and delete files.
You can make requests using the jQuery `.ajax` method for example.

### The File Object

The _File_ object is present in almost all responses and includes these properties:

| Name | Type | Description |
|------|------|-------------|
| name | _string_ | The file name. |
| type | _string_ | File mime type. Example: image/jpeg |
| extension | _string_ | The file extension. Example: jpg |
| url | _string_ | The file url. |
| size | _integer_ | The file size in bytes. | 
| time | _integer_ | The file timestamp. |
| width | _integer_ | The image width. Only for images. |
| height | _integer_ | The image height. Only for images. |
| versions | _object_ | The image versions. Only for images. |
| versions.{version}.name | _string_ | The image version file name. | 
| versions.{version}.url | _string_ | The image version file url. | 
| versions.{version}.size | _integer_ | The image version file size. | 
| versions.{version}.width | _integer_ | The image version width. | 
| versions.{version}.height | _integer_ | The image version height. |  

### List all Files

To retrieve the list of files, send a GET request.

The response will be a JSON object with a key called `files` containing an array of [File](#the-file-object) objects and another key called `total` with the total number of files. Optional include any of the parameters:

- `limit` | _integer_ | Limit the returned files.
- `offset` | _integer_ | Get files from the given offset.

### Upload a File

To upload a file / files, send a POST request with the `file` parameter for a single file and `files[]` for multiple files.

The response will be a JSON object with a key called `files`. The value of this will be an array of [File](#the-file-object)  objects.

### Download a File

To download a file, send a GET request with the `file` parameter (the name of the file). For images include the `version` parameter to only download the specified version.

### Update a File

To update an existing file, send a PUT request with the `file` parameter. By default method is used to crop images, so pass the `width`, `height` and `x`, `y` parameters.

The response will be a JSON object with a key called `file`. The value of this will be [File](#the-file-object)  object.

### Delete a File

To delete a file / files, send a DELETE request with the `file` parameter for a single file and `files[]` for multiple files.

The file / files will be deleted and the response status will be a `204`. This indicates a successful request with no body returned.

> Notice: Browsers don't support PUT and DELETE methods, so include the  `_method` parameter in your requests (`_method=PUT` / `_method=DELETE`). 
