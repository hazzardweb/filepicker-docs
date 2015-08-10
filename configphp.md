# PHP Configuration

- [Introduction](#introduction)
- [Options](#options)

## Introduction

When creating a new instance of `Hazzard\Filepicker\Uploader` you pass a `Hazzard\Config\Repository` that you use to set configuration options:

```php
use Hazzard\Filepicker\Uploader;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;

$uploader = new Uploader($config = new Config, new ImageManager);
```	

Set configuration options:
```php
$config['debug'] = true;
// or
$config->set('upload_dir', __DIR__.'/../files');
```

The second argument is an instance of [Intervention Image](http://image.intervention.io/) which supports two image processing extensions: __GD__ and __Imagick__. The default driver is  __GD__, to change it just pass the configuration as an array directly into the ImageManager instance:

```php
$manager = new ImageManager(array('driver' => 'imagick'));

$uploader = new Uploader($config = new Config, $manager);
```

> Notice: Make sure you have installed [Imagick](https://www.google.com/search?q=install+imagick+php) if you want to use it.

## Options

### debug

If enabled, more detailed error messages will be shown on every error that occurs, otherwise a simple generic error is shown.

- Type: _boolean_
- Default: `false`

### upload_dir

The directory where the files are uploaded. Make sure to use absolute paths and the directory has read/write permissions.

- Type: _string_
- Default: `n/a`
- Example: `__DIR__.'/../files'`

### upload_url

The url to the directory where the files are uploaded.

- Type: _string_
- Default: `n/a`
- Example: `http://localhost/filepicker/files`


### script_url

The url to the file where the ajax requests are sent.

- Type: _string_
- Default: current script
- Example: `http://localhost/filepicker/uploader/index.php`

### min_file_size

The minimum file size required (in Bytes).

- Type: _integer_
- Default: `1`
- Example `1000` (= 1 Kilobyte)

### max_file_size

The maximum file size allowed (in Bytes).

- Type: _integer_
- Default: `n/a`
- Example `10000000` (= 10 Megabyes)

### accept_file_types

The accepted file types. By default the script accepts all file types except `.php` files for security purposes.

- Type: _string_
- Default: `n/a`
- Example: `jpg|png|txt|pdf|mp3`

### accept_file_types_regex

The accepted file types regular expresion.

- Type: _string_
- Default: `n/a`

### image_file_types

Files that should be handled as images. Used for validation and creating image versions.

- Type: _string_
- Default: `gif|jpg|jpeg|png`

### inline_file_types

File types that should be handled as inline files, used when [php_download](#php_download) is enabled.

- Type: _string_
- Default: `gif|jpg|jpeg|png|pdf|txt|js|css`

### max_number_of_files

The maximum number of files allowed to be uploaded to the [upload_dir](#upload_dir)

- Type: _integer_
- Default: `n/a`
- Example: `20`

### php_download

Whether to use direct file links or donwload the files via PHP. 

- Type: _boolean_
- Default: `false`

### min_width, max_width, min_height, max_height

The min/max width/height for image type files.

- Type: _integer_
- Default: `1` / `n\a`

### image_versions

An array with image versions that are created for each uploaded image.
The key of each element will be the version name and the value will be an array contain the version constraints.
The default version will have an empty key.

- Type: _array_ 
- Default: 

```php
array(
	'' => array(
		'auto_orient' => true,
	),
	'thumb' => array(
		'crop' => true,
		'max_width' => 100,
		'max_height' => 100,
	),
),
```

#### auto_orient

Auto orient the image based on EXIF data.

- Type: _boolean_

#### crop

Create a square image instead of just scaling the image version. 

- Type: _boolean_

#### max_width

The image version maximum width.

- Type: _integer_

#### max_height

The image version maximum height.

- Type: _integer_

#### quality

The quality of the image (0 - 100). 

- Type: _integer_
- Default: `90`

#### upload_dir

Custom directory where the image version is created. Make sure to use absolute paths and the directory has read/write permissions. If is the same as the global [upload_dir](#upload_dir) directory then, the image version will be named like this:`filename-version.jpg`.

- Type: _string_
- Default: [upload_dir](#upload_dir) `. '/version'`
- Example: `__DIR__.'/../files'`

#### upload_url

Custom url to the directory where the image version is created.

- Type: _string_
- Default: [upload_url](#upload_url) `. '/version'`
- Example: `http://localhost/filepicker/files`

#### before

Callback fired before creating the image version. The callback has two arguments, `$image` which is an instance of _\Intervention\Image\Image_ (see [Intervention Image](http://image.intervention.io/)) and `$version`, the version name. Returning `false` will cancel the crop.

- Type: _Closure_
- Default: `n/a`

Example:

```php
	/**
	 * 
	 * @param  \Intervention\Image\Image $image 
	 * @param  string $version
	 */
	'before' => function($image, $version) {
		// return false;
	},
```

#### after

Callback fired after creating the image version. Same as the [before](#before) callback, except with no return.

- Type: _Closure_
- Default: `n/a`

### keep_original_image
		
Whether to keep the original image without cropping it. This allows to use the original image for later re-crop.

- Type: _boolean_
- Default: `false`

### sorting_order

The alphabetical sorting order when scaning the directory for files with [scandir](http://php.net/manual/en/function.scandir.php).
Use `0` for ascending order, and `1` for descending order. In PHP 5.4 you can use `SCANDIR_SORT_NONE` for unsorted.

- Type: _integer_
- Default: `0`

### mkdir_mode

The mode used when creating directories with [mkdir](http://php.net/manual/en/function.mkdir.php).

- Type: _integer_
- Default: `0777`

### messages

An array with all error messages.

- Type: _array_
- Default:

```php
array(
	'no_file' => 'No file was uploaded.',
	'max_width' => 'Image exceeds maximum width of %d pixels.',
	'min_width' => 'Image requires a minimum width of %d pixels.',
	'max_height' => 'Image exceeds maximum height of %d pixels.',
	'min_height' => 'Image requires a minimum height of %d pixels.',
	'image_resize' => 'Failed to resize image versions (%s).',
	'upload_failed' => 'Failed to upload the file (error %d).',
	'max_file_size' => 'The file exceeds the maximum allowed file size (limit is %d KB).',
	'min_file_size' => 'The file size is too small.',
	'accept_file_types' => 'The file type is not allowed.',
	'max_number_of_files' => 'Maximum number of files exceeded.',
	'error' => 'Oops! Something went wrong.',
	'abort' => 'The operation was aborted.',
	'404' => 'File not found.',
	'401' => 'Unauthorized.',
),
```
