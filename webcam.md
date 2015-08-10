# Webcam Plugin

- [Basic Usage](#basic-usage)
- [Bootstrap Modal](#bootstrap-modal)
- [Options](#options)

> Notice: Include `assets/js/plugins/webcam.js` before using the webcam plugin.

## Basic Usage

__HTML Setup:__

```markup
<!-- The button that will open the webcam -->
<button type="button" class="webcam">Webcam</button>

<!-- The webcam container -->
<div id="webcam-container" style="display: none;">
	<div class="camera"></div>
	<button type="button" class="snap">Take picture</button>
	<button type="button" class="close-webcam">Close webcam</button>
</div>
```

__JavaScript:__

```javascript
// Filepicker plugin
var FP = $('#filepicker').filePicker({...});

// Webcam plugin call
FilepickerWebcam(FP, {
	container: $('#webcam-container'),
	openButton: $('.webcam'),
	// more options ...
});
```

## Bootstrap Modal

_Requires [Bootstrap](http://getbootstrap.com/)._

__HTML Setup:__

```markup
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
```

__JavaScript:__

```javascript
// Filepicker plugin
var FP = $('#filepicker').filePicker({...});

// Webcam plugin call
FilepickerWebcam(FP, {
	container: $('#webcam-modal'),
	openButton: $('.webcam'),
	modal: true, // Optional, because it auto detects Bootstrap modal support.
	// more options ...
});
```

## Options

### swf

The swf flash file used when the HTML5 webcam is not available (IE & Safari).

- Type: _string_
- Default: `assets/js/plugins/webcam.swf`

## swfSize

The size of the embedded flash file.

- Type: _array[w, h]_
- Default: `[568, 423]`

### container

The jQuery object for the webcam container or Bootstrap modal.  

- Type: _jQuery Object_
- Default: `n/a`
- Example: `$('#webcam-container')` / `$('#webcam-modal')`

### modal

Whether to enable Bootstrap modal. Optional, because it auto detects Bootstrap modal support.

- Type: _boolean_
- Default: `false` / `true` when using Bootstrap modal

### camera

The jQuery object for the element where the webcam preview will be rendered.

- Type: _jQuery Object_
- Default: `container.find('.camera')`
- Example: `$('.camera-preview')`

### openButton

The jQuery object for the button that will open the webcam.

- Type: _jQuery Object_
- Default: `n/a`
- Example: `$('.webcam')`

### snapButton

The jQuery object for the button that will take a picture and upload it.

- Type: _jQuery Object_
- Default: `container.find('.snap')`
- Example: `$('.snap-btn')`

### closeButton

The jQuery object for the button that will close the webcam.

- Type: _jQuery Object_
- Default: `container.find('.close-webcam')`
- Example: `$('.close-webcam-btn')`


