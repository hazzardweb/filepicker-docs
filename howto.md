# HowTo

- [Protect Files](#protect-files)
- [Sort Files](#sort-files)

## Protect Files

If you use Filepicker in a application with users you may want to protect the _files_ directory from users that are not logged in.

First create a `.htaccess` file inside the _files_ directory containing:

	Deny from all 

Next edit `uploader/index.php` and enable the [php_download](configphp.md#php_download) option: 

	$config['php_download'] = true;

Now in before any other event listeners register one with these events: [file.get](handler.md#fileget), [file.download](handler.md#filedownload), [file.delete](handler.md#filedelete) and [crop.before](handler.md#cropbefore):

	$handler->on(array('file.get', 'file.download', 'file.delete', 'crop.before'), function(Event $e) {
		// If the user is Unauthorized, then:
		$e->abort('Unauthorized');
		$e->preventDefault(); // Prevent other events to be fired.
	});

To check if the user is authorized you may include a library from your app, call a function, check if a specific session is set, etc.

_Learn more about the [abort](handler.md#abort) and [preventDefault](handler.md#preventdefault) events._

## Sort Files

To sort the files you can simply register a listener for the [files.get](handler#filesget) event and add use [usort](https://php.net/manual/en/function.usort.php) for example to sort the files.

### Sort by filemtime 
	
	$handler->on('files.get', function(array &$files) {
		usort($files, function($a, $b) {
			if ($a->getMTime() === $b->getMTime()) {
				return 0;
			}
			
			// ">" - newest | "<" - oldest
			return ($a->getMTime() > $b->getMTime()) ? -1 : 1;
		});
	});

### Sort by filesize 

	$handler->on('files.get', function(array &$files) {
		usort($files, function($a, $b) {
			if ($a->getSize () === $b->getSize ()) {
				return 0;
			}
			
			// ">" - descending | "<" - ascending
			return ($a->getSize () > $b->getSize ()) ? -1 : 1;
		});
	});

### Sort by filename

For alphabetically order see the [sorting_order](configphp.md#sorting_order) option.
