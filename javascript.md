# JavaScript Events

- [Introduction](#introduction)
- [Available Events](#available-events)

## Introduction

When calling the jQuery `.filePicker` plugin use the jQuery `.on` method to listen for events. All events have the `filepicker.` prefix:

```javascript
$('#filepicker').filePicker({
	// options ...
})
.on('filepicker.success', function(e, file) {
	alert(file.name + ' has been uploaded.');
})
.on('filepicker.fail', function(e, file) {
	alert(file.name + ' could not be uploaded.');
});
```

## The File Object

When an event is fired the first argument of the callback function is a [jQuery Event Object](https://api.jquery.com/category/events/event-object/) and the second one, a `file` object. This object will contain the file property like name, size, extension etc. Before uploading the file the object will contain just some properties, only after the file is uploaded all properties will be available.

### File Properties Before Upload

| Name | Type | Description |
|------|------|-------------|
| name | _string_ | The file name. |
| type | _string_ | File mime type. Example: image/jpeg |
| extension | _string_ | The file extension. Example: jpg |
| imageFileType | _boolean_ | Whether is a image file type. |
| size | _integer_ | The file size in bytes. | 
| sizeFormatted | _string_ | The file size formated. Example: 1.2 MB |
| context | _jQuery Object_ | The file jQuery object element (if files list is used). | 

### File Properties After Upload

| Name | Type | Description |
|------|------|-------------|
| name | _string_ | The file name. |
| type | _string_ | File mime type. Example: image/jpeg |
| extension | _string_ | The file extension. Example: jpg |
| url | _string_ | The file url. |
| imageFileType | _boolean_ | Whether is a image file type. |
| size | _integer_ | The file size in bytes. | 
| sizeFormatted | _string_ | The file size formated. Example: 1.2 MB |
| time | _integer_ | The file timestamp. |
| timeFormatted | _string_ | The file timestamp formated. Example: 2015-05-20, 10:32 PM| 
| width | _integer_ | The image width. Only for images. |
| height | _integer_ | The image height. Only for images. |
| context | _jQuery Object_ | The file jQuery object element (if files list is used). | 
| origina | _object_ | The original file object [before upload](#file-properties-before-upload). |
| versions | _object_ | The image versions. Only for images. |
| versions.{version}.name | _string_ | The image version file name. | 
| versions.{version}.url | _string_ | The image version file url. | 
| versions.{version}.size | _integer_ | The image version file size. | 
| versions.{version}.width | _integer_ | The image version width. | 
| versions.{version}.height | _integer_ | The image version height. | 

You can use the `e` event object to find the target element or to prevent the event from default:

```javascript
$('#filepicker').filePicker({
	// options ...
})
.on('filepicker.add', function(e, file) {
	e.preventDefault();
});
```

This way you can overwrite the plugin behavior and write your own implementation.

To access the Filepicker internal functions make another call on the element without any options to the the instance:

```javascript
$('#filepicker').filePicker({
	// options ...
})
.on('filepicker.add', function(e, file) {
	// Get the instance:
	var FP = $('#filepicker').filePicker();

	// Access a function:
	FP.getNumberOfFiles();
});
```

Edit `assets/js/jquery.filepicker.js` to find all the available functions.

## Available Events:

### add

Event fired when files are added to the upload queue via the file input or drag & drop.
If prevented from default, the file will not be added to the upload queue.

### start

Event fired when the file upload starts.
If prevented from default, the file will not be sent to the server.

### startall

Event fired when the upload starts for all files.
If prevented from default, the files will not be sent to the server.

### send

Event fired when the file is sent to the server.
If `false` is returned the file transfer will be canceled. Pass a custom error message with `file.errorThrown = 'Custom error.'`.

### progress

Event for upload progress.

Example:

```javascript	
.on('filepicker.progress', function(e, file) {
	var percentage = Math.floor((file.progress.loaded / file.progress.total) * 100);
	console.log(percentage + '%');
}
```

### success

Event fired for successfully uploads.
If prevented from default, the file will not be rendered and added to the files list.

### fail

Event fired for failed uploads.
If prevented from default, the file will not be rendered and added to the files list. Pass a custom error message with `file.errorThrown = 'Custom error.'`.

### always

Event fired for sent uploads (successfully or failed uploads).

### load

Event fired after the files where loaded from the server.

Example:

```javascript
.on('filepicker.load',  function(e, result) {
	console.log(result.files);
});
```

### destroy

Event fired on file deletion.
If prevented from default, the file will not be deleted.

### destroyall

Event fired on file deletions.
If prevented from default, the files will not be deleted.

### destroyed

Event fired for a successfully deletion.

### destroyfailed

Event fired for a failed deletion.

### cropsuccess

Event fired for a successfully image crop.
Available when using the [crop plugin](crop.md).
