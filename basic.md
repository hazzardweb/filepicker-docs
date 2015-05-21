# Basic Example

Single file basic example with no UI.

__CSS:__
	
	<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
	<link rel="stylesheet" href="assets/css/filepicker.css">

__JavaScript:__

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

__HTML:__
	
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
