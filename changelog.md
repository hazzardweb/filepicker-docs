# Release Notes

## v2.0.3 (2017-12-13)

- Fixed bug introduced in the last update

## v2.0.2 (2017-12-12)

- Fixed transparent PNG images
- Fixed url encoded filenames

## v2.0.1 (2017-07-27)

- Fixed ajax method.
- Fixed merge options.
- Fixed orientate image.

## v2.0.0 (2016-05-24)

- Fix file validation (`filepicker.js`).
- Fix array syntax and scope for PHP 5.3 (`BaseUploader.php`).

## v2.0.0-beta.1 (2016-04-25)

In this version Filepicker has been rewritten from scratch. Now it has more options and events, it's easier to customize replace default functionality and integrate. Because of the major changes there is no upgrade guide.

The v1.0 will still have support for bugs and security.

### Added

- Added [sort](configphp.md#sort) option.
- Added [overwrite](configphp.md#overwrite) option.
- Added [image_versions.raw](configphp.md#raw)
- Added [image_versions.width](configphp.md#width)
- Added [image_versions.height](configphp.md#height)
- Added [files.fetch](apiphp.md#filesfetch) and [files.filter](apiphp.md#filesfilter) events.
- Added more JavaScript events, options and methods.
- Added more file icons.

### Changed

- The [events](apiphp.md#available-events) don't use the `Event` class anymore.
- Changed the `upload.fail` event to `upload.error`.
- Changed some [messages](configphp.md#messages).
- The [DELETE](apiphp.md#delete-a-file) in the REST API now returns `200` with an object.
- Passing a request instance to the `Handler::handle` is optional.
- The jQuery `.filePicker` plugin has been rewritten from scratch.
- The `formData` option is now `data`.
- Separate the [UI](ui.md) and [Drop](drop.md) from the core (events and options).
- Changed assets and plugins. 
- Changed the JavaScript templates. 
- Renamed the Webcam plugin to Camera.
- Renamed JavaScript events.
- Switched to [Cropper](https://github.com/fengyuanchen/cropper).

### Removed

- Removed `files.get` and `file.get` events. Use [files.fetch](apiphp.md#filesfetch) and [files.filter](apiphp.md#filesfilter).
- Removed `Event` class to simplify the event system.
- Removed `sorting_order` option. Use [sort](configphp.md#sort).
- Removed `image_versions.crop` Use [image_versions.width](configphp.md#width) and [image_versions.height](configphp.md#height).
- Removed `debug` option in the jQuery plugin.
- Removed Flash support for the Webcam (now Camera) plugin.


## v1.0.5 (2015-11-01)

### Added

- Add `reject_file_types` option to reject certain file types.
    
## v1.0.4 (2015-07-23)

### Fixed

- Fix method call.
- Pass quality param.

## v1.0.3 (2015-06-25)

### Fixed 

- Use PHP 5.3 array syntax.
    
## v1.0.2 (2015-06-19)

### Fixed

- Fix autoload bug from the 1.0.1.
    
## v1.0.1 (2015-06-13)

### Added

- Pass files array.
- Add `Handler::getUploader` method.

### Fixed

- Fix events array.

## v1.0.0 (2015-05-22)

- Initial release.
