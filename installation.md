# Installation

- [Install Filepicker](#install-filepicker)
- [Server Requirements](#server-requirements)

## Install Filepicker

- Extract and copy the files from the archive you have downloaded from CodeCanyon to your server or local server.

- (Optional) Edit `index.php` to configure the [JavaScript options](configjs.md) and `uploader/index.php` to configure the [PHP options](configphp.md).

- Now you can access the Filepicker script in your browser.

> Notice: Make sure the `files` directory has read/write permissions (`0777`). <br>
> If you use [Composer](https://getcomposer.org/), run `composer install`.

### Server Requirements

Filepicker has a few requirements:

- PHP 5.3.3+
- [Fileinfo](https://php.net/manual/ro/book.fileinfo.php)
- [GD](http://php.net/manual/en/book.image.php)
- [Exif](http://php.net/manual/en/book.exif.php) 

### Browser Support

Filepicker supports the following browsers (desktop & mobile):

- Chrome
- Firefox
- Opera
- Safari*
- MS Edge
- IE10+*

_*The camera plugin does not work._ 

Starting with version 47, Chrome requires a secure connection (SSL) in order to use the camera. Read more [here](https://www.chromium.org/Home/chromium-security/deprecating-powerful-features-on-insecure-origins).
