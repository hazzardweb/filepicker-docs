# JavaScript API

- [Events](#events)
- [Handlers](#handlers)
- [Methods](#methods)

## Events

When calling the jQuery `.filePicker` plugin you use the jQuery `.on` method to listen for events. All events end with `.filepicker`.

```javascript
$('#filepicker').filePicker({
    // options ...
})
.on('done.filepicker', function (e, data) {
    console.log(data.files);
})
.on('fail.filepicker', function () {
    alert('Oops! Something went wrong.');
});
```

### add

Event fired when files are added to be uploaded. 

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('add.filepicker', function (e, data) {
    //
})
```

### progress

Event fired on upload progress.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('progress.filepicker', function (e, data) {
    console.log(data.progress); // {total:, loaded:, percentage: }
})
```

### done

Event fired on upload done.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('done.filepicker', function (e, data) {
    console.log(data.files);
})
```

### fail

Event fired on upload fail.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('fail.filepicker', function (e, data) {
    alert(data.errorThrown);
})
```

### always

Event fired after upload done or fail.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('always.filepicker', function (e, data) {
    //
})
```

Additional events can be found for each plugin: [UI](ui.md), [Drop](drop.md), [Crop](crop.md) and [Camera](camera.md).

### The data Object

The `data` object is used to maintain the upload state. It contains the files to be uploaded and methods to upload or abort, etc.

You can access:

- `data.state` A _String_ with the upload state (`pending`, `sending`, `sent`).
- `data.progress` An _Object_ with the upload progress: `{total:, loaded:, percentage: }`.
- `data.send()` Starts the upload and returns a `jQuery Promise Object` or `false` if already started.
- `data.abort()` Aborts the upload.
- `data.files` An array of files to be uploaded and after upload the uploaded files.
- `data.originalFiles` An array of the original files.

### The file Object

Each file from the `data.files` array has the follwoing properties:

#### File Properties Before Upload

| Name | Type | Description |
|------|------|-------------|
| name | _String_ | The file name. |
| type | _String_ | File mime type. Example: image/jpeg |
| extension | _String_ | The file extension. Example: jpg |
| size | _Number_ | The file size in bytes. | 
| sizeFormatted | _String_ | The file size formated. Example: 1.2 MB |
| time | _Number_ | The file timestamp. Example: 1461500968 |
| timeFormatted | _String_ | The file timestamp formated. Example: 2016-04-24, 3:29 PM | 
| timeISOString() | _Function_ | Returns the timestamp as a ISO string. Example: 2016-04-24T14:17:34.000Z |
| imageFile | _Boolean_ | Whether is a image file type. |
| context | _jQuery Object_ | The file jQuery object element (if [UI](ui.md) plugin is used). | 

#### File Properties After Upload

| Name | Type | Description |
|------|------|-------------|
| name | _string_ | The file name. |
| type | _string_ | File mime type. Example: image/jpeg |
| extension | _string_ | The file extension. Example: jpg |
| url | _string_ | The file url. |
| size | _integer_ | The file size in bytes. | 
| sizeFormatted | _string_ | The file size formated. Example: 1.2 MB |
| time | _Number_ | The file timestamp. Example: 1461500968 |
| timeFormatted | _String_ | The file timestamp formated. Example: 2016-04-24, 3:29 PM | 
| timeISOString() | _Function_ | Returns the timestamp as a ISO string. Example: 2016-04-24T14:17:34.000Z |
| width | _integer_ | The image width. Only for images. |
| height | _integer_ | The image height. Only for images. |
| versions | _object_ | The image versions. Only for images. |
| versions.{version}.name | _string_ | The image version file name. | 
| versions.{version}.url | _string_ | The image version file url. | 
| versions.{version}.size | _integer_ | The image version file size. | 
| versions.{version}.width | _integer_ | The image version width. | 
| versions.{version}.height | _integer_ | The image version height. | 
| imageFile | _boolean_ | Whether is a image file type. |
| context | _jQuery Object_ | The file jQuery object element (if [UI](ui.md) plugin is used). | 

When uploading files and [uploadMultiple](configjs.md#uploadMultiple) is disabled (default) you can access the a file property with `data.files[0].name`.

```javascript
.on('done.filepicker', function (e, data) {
    console.log(data.files[0].name);
});
```

However if you [uploadMultiple](configjs.md#uploadMultiple) is enabled you should use a `for` to iterate through the `data.files` array:

```javascript
.on('done.filepicker', function (e, data) {
    for (var i = 0; i < data.files.length; i++) {
        console.log(data.files[i].name);
    }
});
```

## Handlers

To override the default action handlers, pass them as options:

```javascript
$('#filepicker').filePicker({
    // options ...
    add: function (e, data) {
        //
    }
})
```

### add

The file add handler.

```javascript
/** *
 * @param {Object} e
 * @param {Object} data
 */
add: function (e, data) {
```

### progress

The upload progress handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
progress: function (e, data) {},
```

### done

The upload done handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
done: function (e, data) {},
```

### fail

The upload fail handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
fail: function (e, data) {},
```

### always

The upload always handler.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
always: function (e, data) {},
```

### validate

The file validation handler.

```javascript
/**
 * @param  {Object} e
 * @param  {Object} file
 * @return {Boolean}
 */
validate: function (e, file) { }
```

The upload fallback handler. (Old browsers).

### uploadfallback

```javascript
/**
 * @param {Object} e
 * @param {String} message
 */
uploadfallback: function (e, message) { }
```

## Methods

To get the `Filepicker` instance call `.filePicker` again:

```javascript
var FP = $('#filepicker').filePicker();
```

Now you can access the Filepicker methods:

### fetch

Fetch files from the server.

```javascript
FP.fetch({limit: 10, offset: 0})
    .done((result) => {
        console.log(result);
    });
```

### update

Update a file from the server, used to crop images.

```javascript
var options = {
    x:180, y:120,
    width:1400, height:950
};

FP.update('file.jpg', options)
    .done((result) => {
        console.log(result);
    });
```

### delete

Delete a file/files from the server.

```javascript
FP.delete('file.txt')
    .done((result) => {
        console.log(result);
    });

FP.delete(['another.txt', 'one.txt'])
    .done((result) => {
        console.log(result);
    });
```
