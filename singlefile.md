# Single File Example

This examples uses [Bootstrap](http://getbootstrap.com/), includes the [crop](crop.md) and  [webcam](webcam.md) plugins and allows to upload a file at the time.

__CSS:__

```markup
<link rel="stylesheet" href="assets/css/vendor/bootstrap.css">
<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
<link rel="stylesheet" href="assets/css/filepicker.css">
```
__JavaScript:__

```markup
<script src="assets/js/vendor/jquery.js"></script>
<script src="assets/js/vendor/bootstrap.js"></script>
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
			$('.progress-bar').text(percentage + '%').css('width', percentage + '%');
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
				// Show crop if is image.
				$('.crop').show();
			}
		})
		// Crop success event.
		.on('filepicker.cropsuccess', function(e, file) {
			// Update the file back.
			$('.crop').data('file', file);
			console.log(file);
		});

		// Filepicker webcam plugin.
		FilepickerWebcam(FP, {
			container: $('#webcam-modal'),
			openButton: $('.webcam'),
		});

		// Filepicker crop plugin.
		FilepickerCrop(FP, {
			container: $('#crop-modal'),
			cropBtn: $('.crop'),
		});
	});
</script>
```

__HTML:__

```markup
<form id="filepicker" method="POST" enctype="multipart/form-data">
	<div class="btn btn-primary fileinput">
		<span class="glyphicon glyphicon-plus"></span>
		Upload
		<input type="file" name="files[]">
	</div>

	<button type="button" class="btn btn-primary webcam">
		<span class="glyphicon glyphicon-camera"></span>
		Webcam
	</button>

	<button type="button" class="btn btn-info crop" style="display: none">
		<span class="glyphicon glyphicon-edit"></span>
		Crop
	</button>

	<div class="progress" style="display: none;">
		<div class="progress-bar progress-bar-striped active"></div>
	</div>

	<div class="drop-window fade">
		<div class="drop-window-content">
			<h3>Drop files to upload</h3>
		</div>
	</div>
</form>

<ul class="files"></ul>

<!-- Bootstrap webcam modal -->
<div class="modal fade" id="webcam-modal">
	<div class="modal-dialog">
		<div class="modal-content">
			<div class="modal-header">
				<button type="button" class="close" data-dismiss="modal">&times;</button>
				<h4 class="modal-title">Webcam</h4>
			</div>
			<div class="modal-body">
				<div class="camera"></div>
			</div>
			<div class="modal-footer">
				<button type="button" class="btn btn-default close-webcam" data-dismiss="modal">Cancel</button>
				<button type="button" class="btn btn-info snap">Take picture</button>
			</div>
		</div>
	</div>
</div>

<!-- Bootstrap crop modal -->
<div class="modal fade" id="crop-modal">
	<div class="modal-dialog">
		<div class="modal-content">
			<div class="modal-header">
				<button type="button" class="close" data-dismiss="modal">&times;</button>
				<h4 class="modal-title">Crop image</h4>
			</div>
			<div class="modal-body">
				<div class="crop-preview"></div>
			</div>
			<div class="modal-footer">
				<button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
				<button type="button" class="btn btn-primary save" data-loading-text="Saving...">Save</button>
			</div>
		</div>
	</div>
</div>
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
