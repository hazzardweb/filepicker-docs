# Debugging

## Debug Mode

If enabled, any error on the server side will be sent in the response. Set the [debug](configphp.md#debug) option to `true` in `uploader/index.php`. 
```php
$config['debug'] = true;
```

## Browser DevTools

If you get an unexpected error (_Oops! Something went wrong._), you can use browser dev tools to see the errors and response from the server.

In Google Chrome, right-click and select __Inspect__, or use the keyboard shortcut: <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd> (or <kbd>Cmd</kbd>+<kbd>Opt</kbd>+<kbd>I</kbd> on Mac).

Navigate to the __Console__ tab to see potential JavaScript errors and the __Network__ tab to see the requests/responses when you upload files (make sure _Record Network_ Log is on). Watch [this video](http://cl.ly/b1ra) for more.
