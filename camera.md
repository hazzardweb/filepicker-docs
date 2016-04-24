# Camera Plugin

- [Basic Usage](#basic-usage)
- [Bootstrap Modal](#bootstrap-modal)
- [Options](#options)
- [Handlers](#handlers)

The camera plugin allows to capture images with your computer webcam or phone camera. Works only on the browsers that support the [Stream Api](http://caniuse.com/#feat=stream).

## Basic Usage

__HTML:__

```markup
<!-- Camera button -->
<button type="button" class="camera-show">Camera</button>

<!-- Camera container -->
<div class="camera-container" style="display: none;">
    <div class="camera-preview"></div>
    <button type="button" class="camera-hide">Cancel</button>
    <button type="button" class="camera-capture">Take picture</button>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-camera.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['camera'], // <=
    camera: {
        showBtn: $('.camera-show'),
        container: $('.camera-container'),
    }
});
```

## Bootstrap Modal

_Requires [Bootstrap](http://getbootstrap.com/)._

__HTML:__

```markup
<!-- Camera button -->
<button type="button" class="btn btn-info camera-show">Camera</button>

<!-- Camera Modal -->
<div id="camera-modal" class="modal fade" tabindex="-1">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <span class="close" data-dismiss="modal">&times;</span>
                <h4 class="modal-title">Take a picture</h4>
            </div>
            <div class="modal-body">
                <div class="camera-preview"></div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default pull-left camera-hide" 
                    data-dismiss="modal">Cancel</button>
                <button type="button" class="btn btn-success camera-capture">Take picture</button>
            </div>
        </div>
    </div>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-camera.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['camera'], // <=
});
```

## Options

The camera plugin options must be passed inside the `camera` object:

```javascript
$('#filepicker').filePicker({
    // options...
    camera: {
        flipHorizontal: false,
        // more camera options
    }
}
});
```

### container

The jQuery object for the camera container or Bootstrap modal.

- Type: _jQuery Object_
- Default: `$('#camera-modal')`

### preview

The jQuery object for the camera preview.

- Type: _jQuery Object_
- Default: `container.find('.camera-preview')`

### showBtn

The jQuery object for the button that shows the camera container / modal.

- Type: _jQuery Object_
- Default: `element.find('.camera-show')`

### hideBtn

The jQuery object for the button that hides the camera container / modal.

- Type: _jQuery Object_
- Default: `container.find('.camera-hide')`

### captureBtn

The jQuery object for the button that captures the image.

- Type: _jQuery Object_
- Default: `container.find('.camera-capture')`

### constraints

The [getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia#Parameters) video constraints.

- Type: _Object_
- Default: `{}`

### flipHorizontal

Whether to flip the camera horizontal.

- Type: _Boolean_
- Default: `true`

### selectors

An object of selectors.

- Type: _Object_
- Default: 

```javascript
{
    show: '.camera-show',
    hide: '.camera-hide',
    container: '#camera-modal',
    capture: '.camera-capture',
    preview: '.camera-preview'
}
```

## Handlers

### camerafallback

The camera fallback handler.

```javascropt

/**
 * @param {Object} e
 * @param {String} message
 */
camerafallback: function (e, message) { }
```

### cameracapture

The camera capture handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
cameracapture: function (e, data) { }
```

### camerasuccess

The camera success handler.

```javascript
/**
 * @param {Object} e
 * @param {MediaStream} stream
 */
camerasuccess: function (e, stream) { }
```

### cameraerror

The camera error handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} error
 */
cameraerror: function (e, error) { }
```
