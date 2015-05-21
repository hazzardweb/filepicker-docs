# Debugging

- [Debug Mode](#debug-mode)
- [Browser DevTools](#browser-devtools)

## Debug Mode

If enabled, more detailed error messages will be shown on every error that occurs, otherwise a simple generic error is shown (usually with an [error code](debugging.md#error-codes)). To enable use the [debug](configphp.md#debug) option for PHP, and [debug](configjs.md#debug) option for JavaScript. 

## Browser DevTools

If you get some unexpected error that even the debug mode doesn't show it, you can use browser dev tools to see the actual errors or response from server.

In Google Chrome, right-click and select __Inspect Element__, or use the keyboard shortcut: <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd> (or <kbd>Cmd</kbd>+<kbd>Opt</kbd>+<kbd>I</kbd> on Mac).

Navigate to the __Console__ tab to see potential JavaScript errors and the __Network__ tab to see the HTTP requests when you upload a file (make sure _Record Network_ Log is on). Watch [this video](http://cl.ly/b1ra).


#### Error Codes

- `1` - The uploaded file exceeds the [upload_max_filesize](http://php.net/manual/en/ini.core.php#ini.upload-max-filesize) directive in `php.ini`.
- `2` - The uploaded file exceeds the `MAX_FILE_SIZE` directive that was specified in the HTML form.
- `3` - The uploaded file was only partially uploaded.
- `4` - No file was uploaded.
- `6` - Missing a temporary folder.
- `7` - Failed to write file to disk.
- `8` - A PHP extension stopped the file upload.
- `9` - Could not move the file.
