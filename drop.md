# Drop Plugin

- [Usage](#usage)
- [Options](#options)
- [Events](#events)
- [Handlers](#handlers)

The drop plugin allows to drag and drop files to upload. 

## Usage

__HTML:__

```markup
<div class="drop-window">
    <div class="drop-window-content">
        <h3>Drop files to upload</h3>
    </div>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-drop.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['drop'], // <= 
});
```

## Options

### dropZone

- Type: _jQuery Object_
- Default: `$(document)`

### dropWindow

- Type: _jQuery Object_
- Default: `element.find('.drop-window')`


## Events 

If `e.preventDefault()` is called the default action of the event will not be triggered.

### drop

Event fired on file drop.

```javascript
.on('drop.filepicker', function (e, data) {
    console.log(data.files);
});
```

### dragover

Event fired on file dragover.

```javascript
.on('dragover.filepicker', function (e) {
    //
});
```

### dragleave

Event fired on file dragleave.

```javascript
.on('dragleave.filepicker', function (e) {
    //
});
```

## Handlers

The default event handlers have the same name as the events.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
drop: function (e, data) { }

/**
 * @param {Object} e
 */
dragover: function (e) { }

/**
 * @param {Object} e
 */
dragleave: function (e) { }
```
