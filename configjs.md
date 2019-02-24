# jQuery Plugin Configuration

- [Usage](#usage)
- [Options](#options)

## Usage

When you use the jQuery `.filePicker` plugin you can pass an object with options:

```javascript
$('#filepicker').filePicker({
	url: 'uploader/index.php',
    maxFileSize: 10000000,
});
```

## Options

### url

Url to the PHP file where the upload is handled.

- Type: _String_
- Default: `n\a`
- Example `uploader/index.php`

### data

Used to send data to the server.

- Type: _Object_, _Function_
- Default `n/a`
- Example: `{paramA : 'value 1', paramB: 'value 2'}`

Or using a function:

```javascript	
data: function() { 
	return {
		paramA: 'value 1',
		paramB: $('#myinput').val(),
	} 
},
```

On the server side using the [events](apiphp.md) you can access the data with:

```php
$handler->on('upload.before', function ($file) use ($handler) {
    $valueA = $handler->request()->get('paramA'); // <=> $_POST['paramA']
    $valueB = $handler->request()->get('paramB'); // <=> $_POST['paramB']
});
```

### acceptFileTypes

The accepted file types.

- Type: _String_ (regular expression)
- Default: `n/a`
- Example `/(\.|\/)(gif|jpe?g|png)$/i`

> Warning: Make sure to also set the [accept_file_types](configphp.md#accept_file_types) PHP option too!

### imageFileTypes

Files that should be handled as images. Used when displaying the thumbnail preview.

- Type: _String_ (regular expression)
- Default: `/(\.|\/)(gif|jpe?g|png)$/i`

### minFileSize

The minimum file size in bytes required.

- Type: _Number_
- Default: `1`
- Example `1000` (= 1 Kilobyte)

### maxFileSize

The maximum file size in bytes allowed.

- Type: _Number_
- Default: `n/a`
- Example `10000000` (= 10 Megabyes)

### fileInput

The jQuery object / selector for the file input button.

- Type: _jQuery Object_/ _String_
- Default: `input[type="file"]` (inside the container)
- Example: `$('.file-input-btn')`


### uploadMultiple

Whether to upload multiple files in a single request.

- Type: _Boolean_
- Default: `false`

When this is enabled you should not have a "Cancel" button for each file in the ui, because canceling one file will cancel the entire request with all the files. 

### uploadMultipleLimit

The number of files to upload when uploading multiple files in one request.

- Type: _Number_
- Default: `n/a`

### uploadMultipleSize

The max size in bytes of each request when uploading multiple files in one request.

- Type: _Number_
- Default: `n/a`
- Example: `10000000` (= 10 Megabyes)

### parallelUploads

The number of upload requests at the same time.

- Type: _Number_
- Default: `n/a`

### plugins

An array of plugins to be used (`ui`, `crop`, `drop`, `camera`).

- Type: _Array_
- Default: `[]`
- Example: `['ui', 'drop']`

### messages

An object with all error messages.

- Type: _Object_
- Default:

```javascript
{
    uploadFallback: 'The browser does not support file uploads.',
    minFileSize: 'The file must be at least :min KB.',
    maxFileSize: 'The file may not be greater than :max KB.',
    postMaxSize: 'The file exceeds the post max size limit of :max MB.',
    invalidFileType: 'The file type is not allowed.',
    error: 'Oops! Something went wrong.',
    abort: 'The operation was aborted.'
    
    // UI plugin
    processing: 'Processing...',
    deleteFail: 'Could not delete file :file.',

    // Camera plugin
    cameraError: 'Could not access the camera.',
    cameraFallback: 'Your browser does not support the camera feature.',

    // Crop plugin
    cropLoadFail: 'Could not load the image.',
    cropSaveFail: 'Could not save the image.'
}
```
