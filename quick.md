# Quickstart

A very basic example on how to use the script.

__CSS:__

	<link rel="stylesheet" href="assets/css/filepicker.css">

__HTML:__

	<form id="filepicker" method="POST" enctype="multipart/form-data">
		<input type="file" name="files[]">
		
		<div class="drop-window fade">
			<div class="drop-window-content">
				<h3>Drop files to upload</h3>
			</div>
		</div>
	</form>

__JavaScript:__

	<script src="assets/js/vendor/jquery.js"></script>
	<script src="assets/js/jquery.filepicker.js"></script>

	<script>
		jQuery(document).ready(function($) {
			$('#filepicker').filePicker({
				url: 'uploader/index.php',
			})
			.on('filepicker.success', function(e, file) {
				if (file.error) {
					alert(file.error);
				} else {
					alert(file.name);
				}
			});
		});
	</script>

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

	// Handle an incoming HTTP request and send the response.
	$handler->handle(Request::createFromGlobals())->send();
