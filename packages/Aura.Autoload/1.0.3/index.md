---
title: Aura for PHP -- Aura.Autoload 1.0.3
layout: site
active: packages
---

Aura Autoload
=============

[![Build Status](https://travis-ci.org/auraphp/Aura.Autoload.png?branch=develop)](https://travis-ci.org/auraphp/Aura.Autoload)

The Aura Autoload package provides a
[PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)
compliant SPL autoloader implementation for PHP. It also matches the interface
proposed at <https://wiki.php.net/rfc/splclassloader>.

This package is compliant with [PSR-0][], [PSR-1][], and [PSR-2][]. If you
notice compliance oversights, please send a patch via pull request.

[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-1]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md
[PSR-2]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md


Include-Path Usage
------------------

Create an instance of the `Loader` and register it with SPL.

{% highlight php %}
<?php
$loader = require '/path/to/Aura.Autoload/scripts/instance.php';
$loader->register();
?>
{% endhighlight %}

The `Loader` will now look for PSR-0 compliant class names in the
include-path, and throw an `Exception\NotDeclared` if it cannot find one.


Class Prefix Usage
------------------

You can tell the `Loader` to search particular paths for classes with specific
prefixes. The prefixes can be the older PEAR-style class prefixes, or the
newer PHP 5.3 formal namespace prefixes.
    
{% highlight php %}
<?php
// look for all Vendor_* classes in this path:
$loader->add('Vendor_', '/path/to/lib');

// look for Vendor\Package classes in this path:
$loader->add('Vendor\Package\\', '/path/to/Vendor.Package/src');

// additionally, e.g. in testing modes, also look for Vendor\Package
// classes in this path as well:
$loader->add('Vendor\Package\\', '/path/to/Vendor.Package/tests');
?>
{% endhighlight %}

(Note that you should end formal namespace prefixes with a double-backslash,
not a single backslash.)

If the `Loader` cannot find a class in the explicit paths for the specific
prefix given, it will fall back to looking in the include-path.

Note that the path should point to the top of a PSR-0 compliant directory
structure. For example, this ...

{% highlight php %}
<?php
$loader->add('Vendor\Package\\', '/path/to/Vendor.Package/src');
?>
{% endhighlight %}
... assumes a directory structure like this:

    Vendor.Package/
        src/
            Vendor/
                Package/
                    Class.php

You can also set all paths at once as an array.

{% highlight php %}
<?php
$loader->setPaths([
    'Aura\Router\\' => '/path/to/project/Aura.Router/src/',
    'Aura\Di\\'     => '/path/to/project/Aura.Di/src/',
]);
?>
{% endhighlight %}

Exact Class Usage
-----------------

You can tell the `Loader` where a specific individual class is located using
the `setClass()` method.

{% highlight php %}
<?php
// look for the VendorClassName at this location:
$loader->setClass('VendorClassName', '/path/to/VendorClassName.php');
?>
{% endhighlight %}

This allows you to build relatively fast lookup maps of class names to file
names.

You can also set all classes at once using the `setClasses()` method:

{% highlight php %}
<?php
$loader->setClasses([
    'Vendor\Package\Foo' => '/path/to/Vendor/Package/Foo.php',
    'Vendor\Package\Bar' => '/path/to/Vendor/Package/Bar.php',
    'Vendor\Package\Zim' => '/path/to/Vendor/Package/Zim.php',
]);
?>
{% endhighlight %}

Modes
-----

In some cases if you are using Exact class usage and if that file contains
a check for [class_exists](http://php.net/manual/en/function.class-exists.php)
like functions without second parameter, you will end up with Exceptions. 
As we follow <https://wiki.php.net/rfc/splclassloader>, we have three modes 
which will be helpful.

* MODE_SILENT (0) : where no exceptions are thrown under error conditions.
* MODE_NORMAL (1) : where an exception is thrown when a class file is not found.
* MODE_DEBUG  (2) : where an exception is thrown when a class file is not 
  found, or if after loading the file the class is still not declared.


Setting Different Modes
-----------------------

You can either set the mode as 

{% highlight php %}
<?php
$loader->setMode($loader::MODE_SILENT);
?>
{% endhighlight %}

Or by passing the values 0,1, 2.

{% highlight php %}
<?php
$loader->setMode(0);
?>
{% endhighlight %}
