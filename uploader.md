# Uploader

- [Introduction](#introduction)
- [Available Methods](#available-methods)

## Introduction

The Uploader class (`src/Uploader.php`) provides a set of method that you can use to upload, delete, list or download files, etc.

To create a new instance you have to pass an instance of `Hazzard\Config\Repository`, and `Intervention\Image\ImageManager`.

	use Hazzard\Filepicker\Uploader;
	use Intervention\Image\ImageManager;
	use Hazzard\Config\Repository as Config;

	$uploader = new Uploader($config = new Config, new ImageManager);

## Available Methods

> Notice: These are just some of the more common methods, for the full list see `src/Uploader.php`.

### upload

Upload a file from the `$_FILES` global variable or by passing an instance of [UploadedFile](https://github.com/symfony/HttpFoundation/blob/2.6/File/UploadedFile.php). Retruns an instance of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) if it succeeds or throws an [UploadException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/UploadException.php) exception if it fails.

Example:
	
	use Symfony\Component\HttpFoundation\File\Exception\UploadException;
	
	try	{
		$file = $uploader->upload($_FILES['file']);
	} catch (UploadException $e) {
		echo 'Upload error: ' . $e->getMessage();
	}

### get

Returns an array of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) instances.
Has two optional parameters: `$offset = 0` to get the files from the specified offset and `$limit = null` to limit the number of returned files.

Example:
	
	$files = $uploader->get();

### getTotal

Returns the total number of uploaded files.

### download

Returns a file download [Response](https://github.com/symfony/HttpFoundation/blob/2.6/Response.php) if the file is found or throws an [FileNotFoundException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/FileNotFoundException.php) exception.

Example:
	
	use Symfony\Component\HttpFoundation\File\Exception\FileNotFoundException;

	try {
		$response = $uploader->download('file.txt');
		$response->send();
	} catch (FileNotFoundException $e) {
		echo 'File not found.';
	}

For images you can pass a second parameter with the version name.

### getImageVersions

Returns an array of [File](https://github.com/symfony/HttpFoundation/blob/2.6/File/File.php) instances that are the image versions files.

Example:
	
	$files = $uploader->getImageVersions('image.jpg');

### delete

Delete a given file. Returns bool if the file is deleted or not and throws [FileNotFoundException](https://github.com/symfony/HttpFoundation/blob/2.6/File/Exception/FileNotFoundException.php) if the file is not found.

Example:

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

For images you can pass a second parameter with the version name.
