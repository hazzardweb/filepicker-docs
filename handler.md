# HTTP Handler

- [Introduction](#introduction)
- [Events](#events)
- [RESTful API](#restful-api)

## Introduction

The HTTP Handler class (`Hazzard\Filepicker\Http\Handler`) handles incoming HTTP requests and sends a response back to 
the client.

The handler constructor takes as parameter an instance of `Hazzard\Filepicker\Uploader`. You can attach an listen for events and at the end handle the request and send a response.
	
	use Hazzard\Filepicker\Http\Event;
	use Hazzard\Filepicker\Http\Handler;
	use Symfony\Component\HttpFoundation\Request;

	// Create a new handler instance.
	$handler = new Handler($uploader);
	
	// Listen for events.
	$handler->on('upload.before', function(Event $e) {
		// Called before the upload starts.
	});

	// Handle an incoming HTTP request and send the response.
	$handler->handle(Request::createFromGlobals())->send();

## Events

The object `$e` is an instance of `Hazzard\Filepicker\Http\Event` and is a used to pass data to the callback functions, cancel uploads, attach data, etc.

The event class has the following methods:

- getFile() - Returns an instance of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) / [UploadedFile](https://github.com/symfony/HttpFoundation/blob/2.6/File/UploadedFile.php), both extending [SplFileInfo](http://php.net/manual/en/class.splfileinfo.php).
- getImage() Returns an instance of [Image](https://github.com/Intervention/image/blob/master/src/Intervention/Image/Image.php), when available.
- abort($error = null) - Abort the current event (upload, delete, crop, etc), you can pass a message.
- getError() - Get the upload error message.
- setFilename($filename, $originalExtension = true) - Change the filename when uploading a file.
- setData($data) - Attach custom data to the file.

## Available Events

### upload.before

### upload.error

### upload.success

### file.get

### file.download

### file.delete

### crop.before

### crop.after

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

The response will be a JSON object with a key called `files` containing an array of __File__ objects and another key called `total` with the total number of files. Optional include any of the parameters:

- `limit` | _integer_ | Limit the returned files.
- `offset` | _integer_ | Get files from the given offset.

### Upload a File

To upload a file / files, send a POST request with the `file` parameter for a single file and `files[]` for multiple files.

The response will be a JSON object with a key called `files`. The value of this will be an array of __File__ objects.

### Download a File

To download a file, send a GET request with the `file` parameter (the name of the file). For images include the `version` parameter to only download the specified version.

### Update a File

To update an existing file, send a PUT request with the `file` parameter. By default method is used to crop images, so pass the `width`, `height` and `x`, `y` parameters.

The response will be a JSON object with a key called `file`. The value of this will be _File_ object.

### Delete a File

To delete a file / files, send a DELETE request with the `file` parameter for a single file and `files[]` for multiple files.

The file / files will be deleted and the response status will be a `204`. This indicates a successful request with no body returned.

> Notice: Browsers don't support PUT and DELETE methods, so include the  `_method` parameter in your requests (`_method=PUT` / `_method=DELETE`). 
