# jQuery Plugin Configuration

- [Introduction](#introduction)
- [Options](#options)
- [Templates](#templates)

## Introduction

When creating a new instance of the jQuery `.filePicker` plugin you can pass various options:

	$('#filepicker').filePicker({
		debug: true,
		url: 'uploader/index.php',
		autoUpload: false,
	});

## Options

### debug

If enabled, more detailed error messages will be shown on every error that occurs, otherwise a simple generic error is shown.

- Type: _boolean_
- Default: `false`

### url

Url to the PHP file where the upload is handled.

- Type: _string_
- Default: `n\a`
- Example `uploader/index.php`

### formData

Used to pass data to the server.

- Type: _object_, _function_
- Default `n/a`
- Example: `{paramA : 'value 1', paramB: 'value 2'}`

Or using a function:
	
	formData: function() { 
		return {
			paramA: 'value 1',
			paramB: $('#myinput').value(),
		} 
	},

On the server side in any of the handler [events](handler.md) you can access the data with `$_POST` (or `$_GET` when loading the files).
	
	$handler->on('upload.before', function(Event $e) {
		$value = $_POST['paramA'];
	});

> Warning: If you allow users to upload files, don't pass their user id and then save the file based on that. Use [sessions](http://php.net/manual/en/book.session.php) instead.

### autoUpload

Whether to auto upload the file(s) right after selection.

- Type: _boolean_
- Default: `true`

### autoLoad

Whether to auto load the files from the server.

- Type: _boolean_
- Default: `false`

### prependFiles

Add the new files to the beginning of the file list. 

- Type: _boolean_
- Default: `true`

### acceptFileTypes

The accepted file types.

- Type: _string_ (regular expression)
- Default: `n/a`
- Example `/(\.|\/)(gif|jpe?g|png)$/i`

> Warning: Make sure to also set the [accpet_file_types](configphp.md#accept_file_types) PHP option ! This is just so the file is not set to server if the JavaScript validation fails. But it's not a safe way of preventing users from uploading unwanted files to the server.

### imageFileTypes

Files that should be handled as images. Used when displaying the thumbnail preview.

- Type: _string_ (regular expression)
- Default: `/(\.|\/)(gif|jpe?g|png)$/i`

### minFileSize

The minimum file size required (in Bytes).

- Type: _integer_
- Default: `1`
- Example `1000` (= 1 Kilobyte)

### maxFileSize

The maximum file size allowed (in Bytes).

- Type: _integer_
- Default: `n/a`
- Example `100000000` (= 10 Megabyes)

### maxNumberOfFiles

The maximum number of files allowed to be uploaded.

> Warning: Make sure to also set the [max_number_of_files](configphp.md#max_number_of_files) PHP option !

- Type: _integer_
- Default: `n/a`
- Example: `20`

### previewThumbnailSize

The size of the preview thumbnail.

- Type: _array[width, height]_
- Default: `[64, 64]`

### messages

An array with all error messages.

- Type: _object_

Default:

	{
		acceptFileTypes: 'The file type is not allowed.',
		maxFileSize: 'The file exceeds the maximum allowed file size.',
		minFileSize: 'The file size is too small.',
		maxNumberOfFiles: 'Maximum number of files exceeded.',
		error: 'Oops! Something went wrong.',
		browser: 'Please upgrade your browser!',
	}

### fileInput

The jQuery object for the file input button.

- Type: _jQuery Object_
- Default: `element.find('input[type="file"]')`
- Example: `$('.file-input-btn')`

### startBtn

The jQuery object for the _start uploading all queued files_ button.

- Type: _jQuery Object_
- Default: `element.find('.start')`
- Example: `$('.start-all-btn')`

### cancelBtn

The jQuery object for the _cancel all files in the upload queue_ button.

- Type: _jQuery Object_
- Default: `element.find('.cancel')`
- Example: `$('.cancel-all-btn')`

### deleteBtn

The jQuery object for the delete all files button.

- Type: _jQuery Object_
- Default: `element.find('.delete')`
- Example: `$('.delete-all-btn')`

### filesList

The jQuery object for the list / table where the files are appended or prepended.

- Type: _jQuery Object_
- Default: `element.find('.files')`
- Example: `$('.files-list')`

### dropZone

The jQuery object for the drop target.

- Type: _jQuery Object_
- Default: `$(document)`
- Example: `$('.drop-zone')`

### dropWindow

The jQuery object for drop window that will be shown in the [dropZone](#dropZone).

- Type: _jQuery Object_
- Default: `element.find('.drop-window')`
- Example: `$('.other-drop-window')`

### uploadTemplateId

The ID of the template used to render the file before it's uploaded.

- Type: _string_
- Default: `templateUpload`

### downloadTemplateId

The ID of the template used to render the file after it's uploaded.

- Type: _string_
- Default: `templateDownload`

### selectors

An array of selectors used to attach events for the files in the file list.

- Type: _array_

Default:

	{
		cancel: '.cancel',
		start: '.start',
		destroy: '.delete',
		preview: '.preview',
		error: '.error',
		progressbar: '.progress-bar'
	}

## Templates

If you use the script with a [filesList](#fileslist) you can have templates with the HTML that will be appended / prepended to the file list.

There are 2 templates, one used to render the file before it's uploaded and another one to render the file after it's uploaded. You can find examples for both templates in the `index.html` and `basic-list.html`.

Inside the template you can access the [file object](javascript.md#the-file-object) properties, add if statements, etc.

#### Display a property:

	<%= file.name %>

#### If statement:

	<% if (file.error) { %>
		<%= file.error %>
	<% } %>

#### If-else statement:

	<% if (file.url) { %>
		<a href="<%= file.url %>"><%= file.name %></a>
	<% } else { %>
		<%= file.name %>
	<% } %>

_Read more about the templates [here](http://ejohn.org/blog/javascript-micro-templating/)._
