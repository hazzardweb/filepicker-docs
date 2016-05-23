# PHP API

- [Introduction](#introduction)
- [Listen for Events](#listen-for-events)
- [Available Events](#available-events)
- [Uploader Class](#uploader-class)
- [REST API](#rest-api)

## Introduction

The Handler class (`uploader/src/Handler.php`) handles the incoming HTTP requests and sends a response back to 
the client. 

```php
use Hazzard\Filepicker\Handler;
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

// Include composer autoload.
require __DIR__.'/../vendor/autoload.php';

$uploader = new Uploader($config = new Config, new ImageManager);

// Create a new handler instance.
$handler = new Handler($uploader);
```

## Listen for Events

You can use the handler to listen for events where you can rename the file, save it to database or abort the upload:

```php
// Fired before the file upload starts.
$handler->on('upload.before', function ($file) {
    // Here you may rename the file.
    $file->save = 'newfilename';
});

// Fired on upload success.
$handler->on('upload.success', function ($file) {
    // Here you may save the file to databae.
});

// Handle the request.
$handler->handle()->send();
```

If you want to register multiple events with the same callback function you can pass an array of events:

```php
$handler->on(array('upload.before', 'crop.before'), function () {
    throw new \Hazzard\Filepicker\Exception\AbortException('Unauthorized');
});
```

### The $file Object

Most of the events will have a `$file` object with the following methods:

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

The `$file` object is an instance of [UploadedFile](https://github.com/symfony/HttpFoundation/blob/2.6/File/UploadedFile.php) when the file is about to be uploaded (`upload.before` and `upload.error`) and [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) after the file is uploaded. Both classes extend the  [SplFileInfo](http://php.net/manual/en/class.splfileinfo.php) class.

### Renaming Files 

To rename an uploaded file use the [upload.before](#uploadbefore) event:

```php
$handler->on('upload.before', function ($file) {
    $file->save = 'myfile';
    // OR
    $file->saveWithExtension = 'myfile.txt';
});
```

### Attaching Data

You can set the `data` property on the file to attach custom data.

```php
$handler->on('upload.success', function ($file) {
    $file->data = array('id' => '1', 'description' => 'My File');
});
```

### Image Object

In the [crop.before](#cropbefore) and [crop.after](#cropafter) events you will also have access to the `$image` object which in an instance of [Image](https://github.com/Intervention/image/blob/master/src/Intervention/Image/Image.php) and can be used to make additional changes to image files.

If you need the image instance in other events use this (make sure the file is actually an image):

```php
$image = $handler->uploader()->getImageManager()->make($file);
```

## Available Events

### init

Event fired on initialization before any other event. 

```php
$handler->on('init', function () {

});
```

### upload.before

Event fired before the file upload starts. To abort the upload throw an `AbortException` exception:

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\UploadedFile $file
 */
$handler->on('upload.before', function ($file) {
    throw new \Hazzard\Filepicker\Exception\AbortException('Error message!');
});
```

You can also use it to [rename](#renaming-files) the file.

### upload.success

Event fired on upload success. You can use it to save your files into database.

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\UploadedFile $file
 */
$handler->on('upload.success', function ($file) {
    //
});
```

### upload.error

Event fired for a failed upload. Use `$file->errorMessage` to get or set the error message.

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\UploadedFile $file
 */
$handler->on('upload.error', function ($file) {
    //
});
```

### files.fetch

Event fired on when fetching files. You can use it to return the files you want to be listed. See [Fetching Files](#tutorials.md#fetching-files).

```php
/**
 * @param array &$files
 */
$handler->on('files.fetch', function (&$files) {
    $files = array('file1', 'file2', 'file3');
});
```

### files.filter

Event fired on file filtering. See [Filtering Files](#tutorials.md#filtering-files).

```php
/**
* @param array &$files
* @param int   &$total
 */
$handler->on('files.filter', function(array &$files, &$total) {
    
});
```

### file.download

Event fired on file download. To abort the download throw an `AbortException` exception:

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\File $file
 */
$handler->on('file.download', function ($file) {
    throw new \Hazzard\Filepicker\Exception\AbortException('You can`t download this file!');
});
```

### file.delete

Event fired on file deletion. To abort the deletion throw an `AbortException` exception:

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\File $file
 */
$handler->on('file.delete', function ($file) {
    throw new \Hazzard\Filepicker\Exception\AbortException('You can`t delete this file!');
});
```

### crop.before

Event fired before the crop starts. 

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\File $file
 * @param \Intervention\Image\Image $image
 */
$handler->on('crop.before', function ($file, $image) {
    //
});
```

### crop.after

Event fired on crop completion.

```php
/**
 * @param \Symfony\Component\HttpFoundation\File\File $file
 * @param \Intervention\Image\Image $image
 */
$handler->on('crop.after', function ($file, $image) {
    //
});
```

## Uploader Class

The Uploader class (`uploader/src/Uploader.php`) it's used by the Handler and provides a set of method that you can use to upload, delete, list or download files, etc.

To create a new instance you have to pass an instance of `Hazzard\Config\Repository`, and `Intervention\Image\ImageManager`.

```php
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

// Include composer autoload.
require __DIR__.'/../vendor/autoload.php';

$uploader = new Uploader($config = new Config, new ImageManager);
```

### Available Methods

### upload

Upload a file from the `$_FILES` global variable or by passing an instance of [UploadedFile](https://github.com/symfony/HttpFoundation/blob/2.6/File/UploadedFile.php). Retruns an instance of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) if it succeeds or throws an [UploadException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/UploadException.php) exception if it fails.

Example:

```php  
use Symfony\Component\HttpFoundation\File\Exception\UploadException;

try {
    $file = $uploader->upload($_FILES['file']);
} catch (UploadException $e) {
    echo 'Upload error: ' . $e->getMessage();
}
```

### get

Returns an array of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) instances.
Has three optional parameters: `$offset = 0` to get the files from the specified offset, `$limit = null` to limit the number of returned files and `$sort` to [sort](config.php#sort) the files.

Example:
    
```php
$files = $uploader->get();
```

### getTotal

Returns the total number of uploaded files.

### download

Returns a file download [Response](https://github.com/symfony/HttpFoundation/blob/2.6/Response.php) if the file is found or throws an [FileNotFoundException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/FileNotFoundException.php) exception.

Example:

```php
use Symfony\Component\HttpFoundation\File\Exception\FileNotFoundException;

try {
    $response = $uploader->download('file.txt');
    $response->send();
} catch (FileNotFoundException $e) {
    echo 'File not found.';
}
```

For images you can pass a second parameter with the version name.

### getImageVersions

Returns an array of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) instances that are the image versions files.

Example:

```php  
$files = $uploader->getImageVersions('image.jpg');
```

### delete

Delete a given file. Returns bool if the file is deleted or not and throws [FileNotFoundException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/FileNotFoundException.php) if the file is not found.

Example:

```php
use Symfony\Component\HttpFoundation\File\Exception\FileNotFoundException;

try {
    if ($uploader->delete('file.txt')) {
        echo 'File deleted.';
    } else {
        echo 'The file could not be deleted.';
    }
} catch (FileNotFoundException $e) {
    echo 'File not found.';
}
```

For images you can pass a second parameter with the version name.

> Notice: These are just some of the more common methods, for the full list see `uploader/src/Uploader.php`.

## REST API

The REST API allows you to perform requests to the server to upload, update, retrieve and delete files.

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
- `sort` | _integer_ | The [sort](configphp.md#sort) option.

### Upload a File

To upload a file / files, send a POST request with the `file` parameter for a single file and `files[]` for multiple files.

The response will be a JSON object with a key called `files`. The value of this will be an array of [File](#the-file-object) objects.

### Download a File

To download a file, send a GET request with the `file` parameter (the name of the file). For images include the `version` parameter to only download the specified version.

### Update a File

To update an existing file, send a PATCH request with the `file` parameter. By default method is used to crop images, so pass the `width`, `height` and `x`, `y` parameters.

The response will be a JSON object with a key called `file`. The value of this will be [File](#the-file-object)  object.

### Delete a File

To delete a file / files, send a DELETE request with the `file` parameter for a single file and `files[]` for multiple files.

The file / files will be deleted and the response will contain an object with the deleted files.
