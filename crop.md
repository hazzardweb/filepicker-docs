# Crop Plugin

- [Basic Usage](#basic-usage)
- [Bootstrap Modal](#bootstrap-modal)
- [Options](#options)

> Notice: Include `assets/js/plugins/crop.js` before using the crop plugin.

## Basic Usage
	
__HTML Setup:__
	
	<!-- The crop container -->
	<div id="crop-container" style="display: none;">
		<div class="crop-preview"></div>
		<button type="button" class="cancel">Cancel</button>
		<button type="button" class="save">Save</button>
	</div>

__JavaScript:__

	// Filepicker plugin
	var FP = $('#filepicker').filePicker({...});
	
	// Crop plugin call
	FilepickerCrop(FP, {
		container: $('#crop-container'),
		// more options ... 
	});

## Bootstrap Modal

_Requires [Bootstrap](http://getbootstrap.com/)._

__HTML Setup:__

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

__JavaScript:__

	// Filepicker plugin
	var FP = $('#filepicker').filePicker({...});
	
	// Crop plugin call
	FilepickerCrop(FP, {
		container: $('#crop-modal'),
		modal: true, // Optional, because it auto detects Bootstrap modal support.
		// more options ... 
	});

## Options

### aspectRatio

Aspect ratio of width/height.

- Type: _decimal_
- Default: `n/a`
- Example: `1` (for square)

### minSize

Minimum width/height, use 0 for unbounded dimension.

- Type: _array [w, h]_
- Default: `n/a`
- Example: `[150, 250]`

### maxSize

Maximum width/height, use 0 for unbounded dimension.

- Type: _array [w, h]_
- Default: `n/a`
- Example: `[150, 250]`

### setSelect

Set an initial selection area.

- Type: _array [x, y, x2, y2]_
- Default: `n/a`

### container

The jQuery object for the crop container or Bootstrap modal.  

- Type: _jQuery Object_
- Default: `n/a`
- Example: `$('#crop-container')` / `$('#crop-modal')`

### modal

Whether to enable Bootstrap modal. Optional, because it auto detects Bootstrap modal support.

- Type: _boolean_
- Default: `false` / `true` when using Bootstrap modal

### preview

The jQuery object for the element where the image preview will be rendered.

- Type: _jQuery Object_
- Default: `container.find('.crop-preview')`
- Example: `$('.crop-preview')`

### cropBtn

The Query object for the button that will open the crop. When using the uploader with [filesList](configjs.md#fileslist) it will look into the download template for a `.crop` button.

If Filepicker is used without a the [filesList](configjs.md#fileslist), this jQuery object must have the `data-file` attribute set with the file object. Edit `basic.html` to see how you can attach a file object to the button.

- Type: _jQuery Object_
- Default: `filesList.find('.crop')`
- Example: `$('.crop-btn')`

### saveBtn

The jQuery object for the button that will save the cropped image.

- Type: _jQuery Object_
- Default: `container.find('.save')`
- Example: `$('.save-crop-btn')`

### cancelBtn

The jQuery object for the button that will cancel the crop.

- Type: _jQuery Object_
- Default: `container.find('.cancel')`
- Example: `$('.cancel-crop-btn')`
