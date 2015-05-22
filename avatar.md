# Avatar Picker Example

This examples uses [Bootstrap](http://getbootstrap.com/), includes the [crop](crop.md) and  [webcam](webcam.md) plugins and allows to upload an image for your profile avatar.

__CSS:__

	<link rel="stylesheet" href="assets/css/vendor/bootstrap.css">
	<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
	<link rel="stylesheet" href="assets/css/filepicker.css">

__JavaScript:__

	<script>
		jQuery(document).ready(function($) {
			// Initialize the plugin.
			var FP = $('#filepicker').filePicker({
				url: 'uploader/index.php',
				// Only accept images. Make sure to check in PHP too !!
				acceptFileTypes: /(\.|\/)(gif|jpe?g|png)$/i,
			})
			.on('filepicker.add', function(e, file) {
				if (file.error) alert(file.error);
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
				// Update the file.
				$('.crop').data('file', file);
				// Check for file upload error.
				if (file.error) return alert(file.error);
				// Show crop button if is image.
				$('.crop').show();
				// Update the image src.
				$('.avatar').attr('src', file.versions.medium.url +'?'+ new Date().getTime());
			})
			// Crop success event.
			.on('filepicker.cropsuccess', function(e, file) {
				// Update the file back.
				$('.crop').data('file', file);
				// Update the image src.
				$('.avatar').attr('src', file.versions.medium.url +'?'+ new Date().getTime());
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
				aspectRatio: 1, // Square images.
			});
		});
	</script>

__HTML:__
	
	<p><img src="http://placehold.it/280&text=no+image" class="avatar" width="280"></p>

	<form id="filepicker" method="POST" enctype="multipart/form-data">
		<div class="btn btn-primary fileinput">Upload<input type="file" name="files[]"></div>
		<button type="button" class="btn btn-primary webcam">Webcam</button>
		<button type="button" class="btn btn-info crop" style="display: none">Crop</button>
		
		<div class="progress" style="display: none; width: 280px">
			<div class="progress-bar progress-bar-striped active"></div>
		</div>

		<div class="drop-window fade">
			<div class="drop-window-content">
				<h3>Drop files to upload</h3>
			</div>
		</div>
	</form>
	
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

__PHP:__

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
	$config['image_versions.medium'] = array(
		'crop' => true,
		'max_width' => 300,
		'max_height' => 300
	);


	// Handle an incoming HTTP request and send the response.
	$handler->handle(Request::createFromGlobals())->send();
