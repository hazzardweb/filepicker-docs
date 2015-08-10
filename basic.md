## Basic Example

Single file basic example with no UI.

__CSS:__

```markup
<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
<link rel="stylesheet" href="assets/css/filepicker.css">
```

__JavaScript:__

```markup
<script src="assets/js/vendor/jquery.js"></script>
<script src="assets/js/vendor/jquery.Jcrop.js"></script>

<script src="assets/js/jquery.filepicker.js"></script>

<script src="assets/js/plugins/webcam.js"></script>
<script src="assets/js/plugins/crop.js"></script>

<script>
	jQuery(document).ready(function($) {
		// Initialize the plugin.
		var FP = $('#filepicker').filePicker({
			url: 'uploader/index.php',
		})
		.on('filepicker.send', function(e, file) {
			// Show progress when uploading the file.
			$('.progress').show();
		})
		.on('filepicker.progress', function(e, file) {
			// Update progress bar.
			var percentage  = Math.floor((file.progress.loaded / file.progress.total) * 100);
			$('.progress').text(percentage + '%');
		})
		.on('filepicker.success', function(e, file) {
			// Hide progress bar.
			$('.progress').hide();

			if (file.error) {
				alert(file.error);
			} else {
				$('.files').append('<li><a href="' + file.url + '">' + file.name + '</a></li>');
			}

			if (file.imageFileType) {
				// Update the file.
				$('.crop').data('file', file);
				// Show crop button if is image.
				$('.crop').show();
			}
		})
		// Crop success event.
		.on('filepicker.cropsuccess', function(e, file) {
			// Update the file back.
			$('.crop').data('file', file);
		});

		// Filepicker webcam plugin.
		FilepickerWebcam(FP, {
			container: $('.webcam-container'),
			openButton: $('.webcam'),
		});

		// Filepicker crop plugin.
		FilepickerCrop(FP, {
			container: $('.crop-container'),
			cropBtn: $('.crop'),
		});
	});
</script>
```

__HTML:__

```markup	
<form id="filepicker" method="POST" enctype="multipart/form-data">
	<input type="file" name="files[]">
	<button type="button" class="webcam">Webcam</button>
	<button type="button" class="crop" style="display: none">Crop</button>

	<div class="progress" style="display: none;"></div>

	<div class="drop-window fade">
		<div class="drop-window-content">
			<h3>Drop files to upload</h3>
		</div>
	</div>
</form>

<!-- Inline webcam container -->
<div class="webcam-container" style="display: none;">
	<div class="camera"></div>
	<button type="button" class="snap">Take picture</button>
	<button type="button" class="close-webcam">Close webcam</button>
</div>

<!-- Inline crop container -->
<div class="crop-container" style="display: none;">
	<div class="crop-preview"></div>
	<br>
	<button type="button" class="cancel">Cancel</button>
	<button type="button" class="save">Save</button>
</div>

<ul class="files"></ul>
```

__PHP:__

```php
use Hazzard\Filepicker\Uploader;
use Hazzard\Filepicker\Http\Event;
use Hazzard\Filepicker\Http\Handler;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;
use Symfony\Component\HttpFoundation\Request;

// Include the autoloader.
require '../vendor/autoload.php';

// Create a new uploader instance.
$uploader = new Uploader($config = new Config, new ImageManager);

// Create a new http handler instance.
$handler = new Handler($uploader);

// Set some configuration options.
$config['upload_dir'] = __DIR__.'/../files';
$config['upload_url'] = 'files';

// Handle an incoming HTTP request and send the response.
$handler->handle(Request::createFromGlobals())->send();
```

## Basic List Example

Basic list example with no UI.

__CSS:__

```markup
<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
<link rel="stylesheet" href="assets/css/filepicker.css">
```

__JavaScript:__

```markup
<script src="assets/js/vendor/jquery.js"></script>
<script src="assets/js/vendor/jquery.Jcrop.js"></script>

<script src="assets/js/jquery.filepicker.js"></script>

<script src="assets/js/plugins/pagination.js"></script>
<script src="assets/js/plugins/webcam.js"></script>
<script src="assets/js/plugins/crop.js"></script>

<script>
	jQuery(document).ready(function($) {
		// Initialize the plugin.
		var FP = $('#filepicker').filePicker({
			url: 'uploader/index.php',
			filesList: $('.files'),
			autoLoad: true,
		});

		// Filepicker webcam plugin.
		FilepickerWebcam(FP, {
			container: $('.webcam-container'),
			openButton: $('.webcam'),
		});

		// Filepicker crop plugin.
		FilepickerCrop(FP, {
			container: $('.crop-container'),
		});
	});
</script>
```
	
__HTML:__

```markup
<form id="filepicker" method="POST" enctype="multipart/form-data">
	<input type="file" name="files[]" multiple>
	<button type="button" class="webcam">Webcam</button>
	<button type="button" class="start">Start upload</button>
	<button type="button" class="cancel">Cancel upload</button>
	<button type="button" class="delete">Delete all</button>
	<br>

	<!-- Inline webcam container -->
	<div class="webcam-container" style="display: none;">
		<div class="camera"></div>
		<button type="button" class="snap">Take picture</button>
		<button type="button" class="close-webcam">Close webcam</button>
	</div>

	<!-- Inline crop container -->
	<div class="crop-container" style="display: none;">
		<div class="crop-preview"></div>
		<br>
		<button type="button" class="cancel">Cancel</button>
		<button type="button" class="save">Save</button>
	</div>

	<div class="drop-window fade">
		<div class="drop-window-content">
			<h3>Drop files to upload</h3>
		</div>
	</div>
</form>

<br>

<table class="table">
	<thead>
		<tr>
			<th>Preview</th>
			<th>Filename</th>
			<th>Filesize</th>
			<th>Date</th>
			<th>Actions</th>
		</tr>
	</thead>
	<tbody class="files"></tbody>
</table>

<!-- File upload template -->
<script type="text/html" id="templateUpload">
	<tr class="template-upload">
		<td class="preview">
			<span class="icon-file-<%= file.extension %>"></span>
		</td>
		<td>
			<%= file.name %>
			<% if (file.error) { %>
				| <%= file.error %>
			<% } %>
		</td>
		<td colspan="2">
			<%= file.sizeFormatted || '' %> | <div class="progress-bar"></div>
		</td>
		<td>
			<% if ( ! options.autoUpload && ! file.error) { %>
				<button type="button" class="start">Start</button>
			<% } %>
			<button type="button" class="cancel">Cancel</button>
		</td>
	</tr>
</script>

<!-- File download template -->
<script type="text/html" id="templateDownload">
	<tr class="template-download">
		<td class="preview">
			<% if (file.versions && file.versions.thumb) { %>
				<a href="<%= file.url %>">
					<img src="<%= file.versions.thumb.url %>" width="64" height="64"></a>
				</a>
			<% } else { %>
				<span class="icon-file-<%= file.extension %>"></span>
			<% } %>
		</td>
		<td>
			<% if (file.url) { %>
				<a href="<%= file.url %>" target="_blank"><%= file.name %></a>
			<% } else { %>
				<%= file.name %>
			<% } %>
			<% if (file.error) { %>
				| <%= file.error %>
			<% } %>
		</td>
		<td><%= file.sizeFormatted %></td>
		<td><%= file.timeFormatted %></td>
		<td>
			<% if (file.imageFileType && ! file.error) { %>
				<button type="button" class="crop">Crop</button>
			<% } %>
			<% if (file.error) { %>
				<button type="button" class="cancel">Cancel</button>
			<% } else { %>
				<button type="button" class="delete" title="Delete">Delete</button>
			<% } %>
		</td>
	</tr>
</script>
```

__PHP:__

```php
use Hazzard\Filepicker\Uploader;
use Hazzard\Filepicker\Http\Event;
use Hazzard\Filepicker\Http\Handler;
use Intervention\Image\ImageManager;
use Hazzard\Config\Repository as Config;
use Symfony\Component\HttpFoundation\Request;

// Include the autoloader.
require '../vendor/autoload.php';

// Create a new uploader instance.
$uploader = new Uploader($config = new Config, new ImageManager);

// Create a new http handler instance.
$handler = new Handler($uploader);

// Set some configuration options.
$config['upload_dir'] = __DIR__.'/../files';
$config['upload_url'] = 'files';

// Handle an incoming HTTP request and send the response.
$handler->handle(Request::createFromGlobals())->send();
```
