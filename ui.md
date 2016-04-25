# UI Plugin

- [Usage](#usage)
- [Options](#options)
- [Templates](#templates)
- [Events](#events)
- [Handlers](#handlers)

The UI plugin adds the file list with auto loading files from server and pagination.

## Usage

__HTML:__

```markup
<div id="filepicker">
    <!-- Button Bar -->
    <div class="button-bar">
        <input type="file" name="files[]" multiple>
        <button type="button" class="delete-all">Delete all</button>
    </div>

    <!-- Files -->
    <table>
        <thead>
            <tr>
                <th class="column-preview">Preview</th>
                <th class="column-name">Name</th>
                <th class="column-size">Size</th>
                <th class="column-date">Modified</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody class="files"></tbody>
    </table>
    
    <!-- Pagination -->
    <div class="pagination-container text-center"></div>
</div>
```

__JavaScript:__

```markup
<script src="assets/js/filepicker-ui.js"></script>
```

```javascript
$('#filepicker').filePicker({
    // options...
    plugins: ['ui'], // <=
});
```

```markup
<!-- Upload Template -->
<script type="text/x-tmpl" id="uploadTemplate">
    <tr class="upload-template">
        <td class="column-preview">
            <div class="preview">
                <span class="fa file-icon-{%= o.file.extension %}"></span>
            </div>
        </td>
        <td class="column-name">
            <p class="name">{%= o.file.name %}</p>
            <span class="text-danger error">{%= o.file.error || '' %}</span>
        </td>
        <td colspan="2">
            <p>{%= o.file.sizeFormatted || '' %}</p>
            <div class="progress">
                <div class="progress-bar progress-bar-striped active"></div>
            </div>
        </td>
        <td>
            {% if (!o.file.autoUpload && !o.file.error) { %}
                <a href="#" class="start">Upload</a>
            {% } %}
            <a href="#" class="cancel">Cancel</a>
        </td>
    </tr>
</script><!-- end of #uploadTemplate -->

<!-- Download Template -->
<script type="text/x-tmpl" id="downloadTemplate">
    {% o.timestamp = function (src) {
        return (src += (src.indexOf('?') > -1 ? '&' : '?') + new Date().getTime());
    }; %}
    <tr class="download-template">
        <td class="column-preview">
            <div class="preview">
                {% if (o.file.versions && o.file.versions.thumb) { %}
                    <a href="{%= o.file.url %}" target="_blank">
                        <img src="{%= o.timestamp(o.file.versions.thumb.url) %}" width="64" height="64"></a>
                    </a>
                {% } else { %}
                    <span class="fa file-icon-{%= o.file.extension %}"></span>
                {% } %}
            </div>
        </td>
        <td class="column-name">
            <p class="name">
                {% if (o.file.url) { %}
                    <a href="{%= o.file.url %}" target="_blank">{%= o.file.name %}</a>
                {% } else { %}
                    {%= o.file.name %}
                {% } %}
            </p>
            {% if (o.file.error) { %}
                <span class="text-danger">{%= o.file.error %}</span>
            {% } %}
        </td>
        <td class="column-size"><p>{%= o.file.sizeFormatted %}</p></td>
        <td class="column-date">
            {% if (o.file.time) { %}
                <time datetime="{%= o.file.timeISOString() %}">
                    {%= o.file.timeFormatted %}
                </time>
            {% } %}
        </td>
        <td>
            {% if (o.file.imageFile && !o.file.error) { %}
                <a href="#" class="crop">Crop</a>
            {% } %}
            {% if (o.file.error) { %}
                <a href="#" class="cancel">Cancel</a>
            {% } else { %}
                <a href="#" class="delete">Delete</a>
            {% } %}
        </td>
    </tr>
</script><!-- end of #downloadTemplate -->

<!-- Pagination Template -->
<script type="text/x-tmpl" id="paginationTemplate">
    {% if (o.lastPage > 1) { %}
        <ul class="pagination pagination-sm">
            <li {% if (o.currentPage === 1) { %} class="disabled" {% } %}>
                <a href="#!page={%= o.prevPage %}" data-page="{%= o.prevPage %}" title="Previous">&laquo;</a>
            </li>

            {% if (o.firstAdjacentPage > 1) { %}
                <li><a href="#!page=1" data-page="1">1</a></li>
                {% if (o.firstAdjacentPage > 2) { %}
                   <li class="disabled"><a>...</a></li>
                {% } %}
            {% } %}

            {% for (var i = o.firstAdjacentPage; i <= o.lastAdjacentPage; i++) { %}
                <li {% if (o.currentPage === i) { %} class="active" {% } %}>
                    <a href="#!page={%= i %}" data-page="{%= i %}">{%= i %}</a>
                </li>
            {% } %}

            {% if (o.lastAdjacentPage < o.lastPage) { %}
                {% if (o.lastAdjacentPage < o.lastPage - 1) { %}
                    <li class="disabled"><a>...</a></li>
                {% } %}
                <li><a href="#!page={%= o.lastPage %}" data-page="{%= o.lastPage %}">{%= o.lastPage %}</a></li>
            {% } %}

            <li {% if (o.currentPage === o.lastPage) { %} class="disabled" {% } %}>
                <a href="#!page={%= o.nextPage %}" data-page="{%= o.nextPage %}" title="Next">&raquo</a>
            </li>
        </ul>
    {% } %}
</script><!-- end of #paginationTemplate -->
```

## Options

### autoLoad

Whether to auto load the files from server.

- Type: _Boolean_
- Default: `true`

### autoUpload

Whether to auto upload the files.

- Type: _Boolean_
- Default: `true`

### perPage

How many files to load per page. Set to `null` to disable.

- Type: _Number_
- Default: `15`

### refreshPagination

Whether to refresh the pagination after each upload.

- Type: _Boolean_
- Default: `false`

### prependFiles

Whether to prepend or append the uploaded files.

- Type: _Boolean_
- Default: `true`

### previewThumbnailSize

The thubmnail canvas preview size.

- Type: _Array_
- Default: `[64, 64]`

### timeago

Whether to use the [timeago](http://timeago.yarp.com/) plugin for dates.

- Type: _Boolean_
- Default: `false`

### startAll

The jQuery object for the start all button.

- Type: _jQuery Object_
- Default: `element.find('.start-all')`

### cancelAll

The jQuery object for the cancel all button.

- Type: _jQuery Object_
- Default: `element.find('.cancel-all')`

### deleteAll

The jQuery object for the delete all button.

- Type: _jQuery Object_
- Default: `element.find('.delete-all')`

### filesList

The jQuery object for file list.

- Type: _jQuery Object_
- Default: `element.find('.files')`

### pagination

The jQuery object for the pagination container.

- Type: _jQuery Object_
- Default: `element.find('.pagination-container')`

### uploadTemplateId

The id of the template of the file before upload.

- Type: _String_
- Default: `uploadTemplate`

### downloadTemplateId

The id of the template of the file after upload.

- Type: _String_
- Default: `downloadTemplate`

### paginationTemplateId

The pagination template id.

- Type: _String_
- Default: `paginationTemplate`

### selectors

An object of selectors.

- Type: _Object_
- Default: 

```javascript
{
    filesList: '.files',
    preview: '.preview',
    progress: '.progress-bar',
    error: '.error',
    start: '.start',
    cancel: '.cancel',
    'delete': '.delete',
    startAll: '.start-all',
    cancelAll: '.cancel-all',
    deleteAll: '.delete-all',
    pagination: '.pagination-container'
}
```

## Templates

The UI plugin has three templates. Inside the template you can access the file object properties, add if statements, etc.

### Display a property

```javascript
{%= o.file.name %}
```

### If statement

```javascript
{% if (o.file.error) { %}
    <span class="text-danger">{%= o.file.error %}</span>
{% } %}
```

### If-else statement

```javascript
{% if (o.file.url) { %}
    <a href="{%= o.file.url %}">{%= o.file.name %}</a>
{% } else { %}
    {%= o.file.name %}
{% } %}
```

Read more about the [JavaScript Templates](https://github.com/blueimp/JavaScript-Templates).

## Events

### load

Event fired after loading the files. Ony if `autoLoad` is enabled.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('always.filepicker', function (e, data) {
    //
})
```

### start

Event fired clicking on the start button for an individual file.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('start.filepicker', function (e, data) {
    //
})
```

### cancel

Event fired clicking on the cancel button for an individual file.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('cancel.filepicker', function (e, data) {
    //
})
```

### delete

Event fired clicking on the delete button for an individual file.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 */
.on('delete.filepicker', function (e, data) {
    //
})
```

### deletedone

Event fired on delete done.


```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 */
.on('deletedone.filepicker', function (e, data) {
    //
})
```

### deletefail

Event fired on delete fail.

```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 */
.on('deletedone.filepicker', function (e, data) {
    //
})
```

### startall

Event fired clicking on the start all button.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 */
.on('startall.filepicker', function (e) {
    //
})
```

### cancelall

Event fired clicking on the cancel all button.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 */
.on('cancelall.filepicker', function (e) {
    //
})
```

### deleteall

Event fired clicking on the delete all button.

If `e.preventDefault()` is called the default action of the event will not be triggered.

```javascript
/**
 * @param {Object} e
 */
.on('deleteall.filepicker', function (e) {
    //
})
```

### renderdone

Event fired after the file template render is done.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
.on('renderdone.filepicker', function (e, data) {
    //
})
```

## Handlers

### load

The handler for loading files from server.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
load: function (e, data) { }
```

### start

The handler when clicking on the start button for an individual file.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
start: function (e, data) { }
```

### cancel

The handler when clicking on the cancel button for an individual file.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
cancel: function (e, data) { }
```

### delete

The handler when clicking on the delete button for an individual file.

```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 */
'delete': function (e, data) { }
```

### deletedone

The handler for delete done.

```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 * @param {Object} jqXHR
 */
deletedone: function (e, data, jqXHR) { }
```

### deletefail

The handler for delete fail.

```javascript
/**
 * @param {Object} e
 * @param {Object} data {context:, filename:}
 */
deletefail: function (e, data) { }
```

### startall

The handler when clicking on the start all button.

```javascript
/**
 * @param {Object} e
 */
startall: function (e) { }
```

### cancelall

The handler when clicking on the cancel all button.

```javascript
/**
 * @param {Object} e
 */
cancelall: function (e) { }
```

### deleteall

The handler when clicking on the delete all button.

```javascript
/**
 * @param {Object} e
 */
deleteall: function (e) { }
```

### renderupload

The handler for rendering files before upload.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
renderupload: function (e, data) { }
```

### renderdownload

The handler for rendering files after upload.

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
renderdownload: function (e, data) { }
```

### renderdone

The handler on render done (`renderupload` / `renderdownload`).

```javascript
/**
 * @param {Object} e
 * @param {Object} data
 */
renderdone: function (e, data) { }
```

### renderpagination

The handler for rendering the pagination.

```javascript
/**
 * @param {Object} e
 * @param {Objct} data
 */
renderpagination: function (e, data) { }
```

<style>.language-markup { max-height: 300px; }</style>
