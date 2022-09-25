phpMyAdmin on Electron
======================

This simple project allows to run phpMyAdmin inside Electron.
It's simply a default Electron build (see https://github.com/electron/electron-quick-start)
with gulp-connect-php, needed to run PHP.

You need npm and composer installed on your machine to get this working.

![screenshot](https://cloud.githubusercontent.com/assets/179866/24585754/4e086bc4-1792-11e7-9d0c-28791a04fede.png)

Installation
------------

* clone this repository
* run `npm install`
* run `composer install --no-dev`
* enter in `vendor/phpmyadmin/phpmyadmin` and run `composer install --no-dev`
* in the same dir, you need to apply the patch explained below

Apply a patch to get js working
-------------------------------

There's an issue with get jQuery working with electron:

http://stackoverflow.com/questions/32621988/electron-jquery-is-not-defined

You need to implement the proposed solution:

Create a file `vendor/phpmyadmin/phpmyadmin/js/fix_module_before.js` with the content:

```
// see http://stackoverflow.com/a/37480521/369194
if (typeof module === 'object') {window.module = module; module = undefined;}
```

Create a file `vendor/phpmyadmin/phpmyadmin/js/fix_module_after.js` with the content:

```
// see http://stackoverflow.com/a/37480521/369194
if (window.module) module = window.module;
```

Then, edit `vendor/phpmyadmin/phpmyadmin/libraries/Header.php` and call above files:

``` php
$this->_scripts->addFile('fix_module_before.js');  // this is a line to add
$this->_scripts->addFile('jquery/jquery.min.js');  // this is an existing line
// ...
$this->_scripts->addFile('jquery/jquery.debounce-1.0.5.js');  // this is an existing line
$this->_scripts->addFile('fix_module_after.js');  // this is a line to add
$this->_scripts->addFile('menu-resizer.js');  // this is an existing line

```

I know, this workaround is a bit ugly.
I'll try to open a PR to phpmyadmin repo and see if they accept it (since it's not
affecting classic web behavior)


Execution
---------

Run `npm start`
