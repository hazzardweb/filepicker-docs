# Crop Plugin

- [Basic Usage](#basic-usage)
- [Bootstrap Modal](#bootstrap-modal)
- [Options](#options)
- [Handlers](#handlers)

The crop plugins allows to crop, rotate and flip images.

## Basic Usage
	
__HTML:__
	
```markup
<!-- Crop container -->
<div class="crop-container" style="display: none;">
    <div class="crop-loading">Loading image...</div>
    <div class="crop-preview"></div>

    <p>
        <button type="button" class="crop-rotate-left" title="Rotate left">&#8592;</button>
        <button type="button" class="crop-flip-horizontal" title="Flip horizontal">&#8596;</button>
        <!-- <button type="button" class="crop-flip-vertical" title="Flip vertical">&#8597;</button> -->
        <button type="button" class="crop-rotate-right" title="Rotate right">&#8594;</button>
    </p>
    <p>
        <button type="button" class="crop-hide">Cancel</button>
        <button type="button" class="crop-save">Save</button>
    </p>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-crop.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['crop'], // <=
    crop: {
        container: $('.crop-container')
    }
});
```

## Bootstrap Modal

_Requires [Bootstrap](http://getbootstrap.com/)._

__HTML:__

```markup
<!-- Crop Modal -->
<div id="crop-modal" class="modal fade" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <span class="close" data-dismiss="modal">&times;</span>
                <h4 class="modal-title">Make a selection</h4>
            </div>
            <div class="modal-body">
                <div class="alert alert-warning crop-loading">Loading image...</div>
                <div class="crop-preview"></div>
            </div>
            <div class="modal-footer">
                <div class="crop-rotate">
                    <button type="button" class="btn btn-default btn-sm crop-rotate-left" title="Rotate left">
                        <i class="fa fa-undo"></i>
                    </button>
                    <button type="button" class="btn btn-default btn-sm crop-flip-horizontal" title="Flip horizontal">
                        <i class="fa fa-arrows-h"></i>
                    </button>
                    <!-- <button type="button" class="btn btn-default btn-sm crop-flip-vertical" title="Flip vertical">
                        <i class="fa fa-arrows-v"></i>
                    </button> -->
                    <button type="button" class="btn btn-default btn-sm crop-rotate-right" title="Rotate right">
                        <i class="fa fa-repeat"></i>
                    </button>
                </div>
                <button type="button" class="btn btn-default pull-left" data-dismiss="modal">Cancel</button>
                <button type="button" class="btn btn-success crop-save" data-loading-text="Saving...">Save</button>
            </div>
        </div>
    </div>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-crop.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['crop'], // <=
});
```

If you are not using the [UI](ui.md) plugin (with the file list) to show the crop container / modal call:

```javascript
var fileUrl = 'files/image.jpg';
$('#filepicker').filePicker().plugins.crop.show(fileUrl);
```

See the avatar example (`avatar.php`).

## Options

The crop plugin options must be passed inside the `crop` object:

```javascript
$('#filepicker').filePicker({
    // options...
    crop: {
        aspectRatio: 1,
        // more crop options
    }
}
});
```
### aspectRatio

The the aspect ratio of the crop box. By default, the crop box is free ratio.

- Type: Number_
- Default: `n\a`
- Example: `1`, `16 / 9`

### container

The jQuery object for the crop container or Bootstrap modal.

- Type: _jQuery Object_
- Default: `$('#crop-modal')`

### _preview

The jQuery object for the crop preview.

- Type: _jQuery Object_
- Default: `container.find('.crop-preview')`

### showBtn

The jQuery object to show the crop container / modal.

- Type: _jQuery Object_
- Default: `n/a`

### hideBtn

The jQuery object to hide the crop container / modal.

- Type: _jQuery Object_
- Default: `container.find('.crop-hide')`

### saveBtn

The jQuery object for the save button.

- Type: _jQuery Object_
- Default: `container.find('.crop-save')`

### rotateLeftBtn

The jQuery object for the rotate left button.

- Type: _jQuery Object_
- Default: `container.find('.crop-rotate-left')`

### rotateRightBtn

The jQuery object for the rotate right button.

- Type: _jQuery Object_
- Default: `container.find('.crop-rotate-right')`

### flipHorizontal

The jQuery object for the flip horizontal button.

- Type: _jQuery Object_
- Default: `container.find('.crop-flip-horizontal')`

### flipVertical

The jQuery object for the flip vertical button.

- Type: _jQuery Object_
- Default: `container.find('.crop-flip-vertical')`

### loading

The jQuery object for the loading alert.

- Type: _jQuery Object_
- Default: `container.find('.crop-loading')`

### checkCrossOrigin

- Type: _Boolean_
- Default: `true`

Check if the current image is a cross-origin image.

For more options see [Cropper](https://github.com/fengyuanchen/cropper#options).

### selectors

An object of selectors.

- Type: _Object_
- Default: 

```javascript
{
    crop: '.crop',
    container: '#crop-modal',
    loading: '.crop-loading',
    preview: '.crop-preview',
    save: '.crop-save',
    hide: '.crop-hide',
    rotateLeft: '.crop-rotate-left',
    rotateRight: '.crop-rotate-right',
    flipHorizontal: '.crop-flip-horizontal',
    flipVertical: '.crop-flip-vertical'
}
```

## Handlers

### cropsave

```javascript
/**
 * The crop save done handler.
 *
 * @param {Object} e
 * @param {Object} file
 * @param {Object} jqXHR
 */
cropsave: function (e, file) { }
```

### cropsavefail

The crop save fail handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} jqXHR
 */
cropsavefail: function (e, jqXHR) { }
```

### cropsavealways

The crop save always handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data|jqXHR
 */
cropsavealways: function () { }
```

### cropload

The crop image load handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} image
 */
cropload: function (e, image) { }
```

### croploadfail

The crop image load fail handler.

```javascript
/**
 * @param {Object} e
 */
croploadfail: function () { }
```

### cropper

The cropper ready handler. Can be used to configure the [Cropper](https://github.com/fengyuanchen/cropper).

```javascript
/**
 * @param {Object} e
 * @param {Object} image
 */
cropper: function (e, image) { }
```
