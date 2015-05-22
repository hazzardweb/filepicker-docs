# Full Example

This examples uses [Bootstrap](http://getbootstrap.com/) and includes all three plugins ([crop](crop.md), [webcam](webcam.md), [pagination](pagination.md)).

__CSS:__

	<link rel="stylesheet" href="assets/css/vendor/bootstrap.css">
	<link rel="stylesheet" href="assets/css/vendor/jquery.Jcrop.css">
	<link rel="stylesheet" href="assets/css/filepicker.css">
	<link rel="stylesheet" href="assets/css/file-icons.css">

__JavaScript:__

	<script src="assets/js/vendor/jquery.js"></script>
	<script src="assets/js/vendor/bootstrap.js"></script>
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
			});

			// Filepicker pagination plugin.
			FilepickerPagination(FP, $('.pagination'));

			// Filepicker webcam plugin.
			FilepickerWebcam(FP, {
				container: $('#webcam-modal'),
				openButton: $('.webcam'),
			});

			// Filepicker crop plugin.
			FilepickerCrop(FP, {
				container: $('#crop-modal'),
			});
		});
	</script>

__HTML:__
	
	<form id="filepicker" method="POST" enctype="multipart/form-data">
		<div class="btn btn-primary fileinput">Add files <input type="file" name="files[]" multiple></div>
		<button type="button" class="btn btn-primary webcam">Webcam</button>
		<button type="button" class="btn btn-info start">Start upload</button>
		<button type="button" class="btn btn-warning cancel">Cancel upload</button>
		<button type="button" class="btn btn-danger delete">Delete all</button>
		<div class="drop-window fade">
			<div class="drop-window-content">
				<h3>Drop files to upload</h3>
			</div>
		</div>
	</form>
	
	<!-- Files List -->
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
	
	<!-- Pagination -->
	<ul class="pagination pagination-sm"></ul>

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
	
	<!-- File upload template -->
	<script type="text/html" id="templateUpload">
		<tr class="fade template-upload">
			<td>
				<div class="preview">
					<span class="icon-file-<%= file.extension %>"></span>
				</div>
			</td>
			<td>
				<p class="name"><%= file.name %></p>
				<span class="text-danger error">
					<% if (file.error) { %>
						<%= file.error %>
					<% } %>
				</span>
			</td>
			<td colspan="2">
				<p><%= file.sizeFormatted || '' %></p>
				<div class="progress">
					<div class="progress-bar progress-bar-striped active"></div>
				</div>
			</td>
			<td>
				<% if ( ! options.autoUpload && ! file.error) { %>
					<button type="button" class="btn btn-info btn-sm start" title="Upload this file">
						<span class="glyphicon glyphicon-upload"></span>
					</button>
				<% } %>
				<button type="button" class="btn btn-warning btn-sm cancel" title="Cancel this file">
					<span class="glyphicon glyphicon-ban-circle"></span>
				</button>
			</td>
		</tr>
	</script>

	<!-- File download template -->
	<script type="text/html" id="templateDownload">
		<tr class="fade template-download">
			<td>
				<div class="preview">
					<% if (file.versions && file.versions.thumb) { %>
						<a href="<%= file.url %>" target="_blank">
							<img src="<%= file.versions.thumb.url %>" width="64" height="64"></a>
						</a>
					<% } else { %>
						<span class="icon-file-<%= file.extension %>"></span>
					<% } %>
				</div>
			</td>
			<td>
				<p class="name">
					<% if (file.url) { %>
						<a href="<%= file.url %>" target="_blank"><%= file.name %></a>
					<% } else { %>
						<%= file.name %>
					<% } %>
				</p>
				<% if (file.error) { %>
					<span class="text-danger"><%= file.error %></span>
				<% } %>
			</td>
			<td><p><%= file.sizeFormatted %></p></td>
			<td><%= file.timeFormatted %></td>
			<td>
				<% if (file.imageFileType && ! file.error) { %>
					<button type="button" class="btn btn-info btn-sm crop" title="Crop this image">
						<span class="glyphicon glyphicon-edit"></span>
					</button>
				<% } else {  %>
					<button type="button" style="visibility:hidden;width:32px"></button>
				<% } %>
				<% if (file.error) { %>
					<button type="button" class="btn btn-warning btn-sm cancel" title="Cancel this file">
						<span class="glyphicon glyphicon-ban-circle"></span>
					</button>
				<% } else { %>
					<button type="button" class="btn btn-danger btn-sm delete" title="Delete this file">
						<span class="glyphicon glyphicon-trash"></span>
					</button>
				<% } %>
			</td>
		</tr>
	</script>
