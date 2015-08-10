# Pagination Plugin

> Notice: Include `assets/js/plugins/pagination.js` before using the pagination plugin.

__HTML Setup:__

```markup
<div class="text-center">
    <ul class="pagination pagination-sm"></ul>
</div>
```

__JavaScript:__

```javascript
// Filepicker pagination plugin.
FilepickerPagination(FP, $('.pagination'));
```

The `FilepickerPagination` function can take a third parameter for how many files you want per page. The default value is 20.
