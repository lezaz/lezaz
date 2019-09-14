---
layout: post
title:  "How to change image quality in WordPress"
permalink: blog/change-wordpress-image-quality
categories: WordPress
---

WordPress by default compresses and optimizes images for web-use.

However, in some cases you may want to change the actual compression level of your images.

To do that, you can simply add a single line in your theme's `functions.php` file:

```php
<?php add_filter( 'jpeg_quality', function() { return 100;} ); ?>
```

If you're on PHP 5.2 then you can do the same thing like this:

```php
<?php

function my_custom_jpeg_quality() {
    return 100;
}
add_filter( 'jpeg_quality', 'my_custom_jpeg_quality' );

?>
```

The above will allow you to have your images full-quality, but you can change the number to whatever you want. :)

Using a compression of about 90% will decrease the size of your images while the quality will still be quite high, and if you use a number between 80-85% the quality loss will be negligible in most cases and the speed increase for your visitors will be great.

These changes will only apply to future images, so if you want to apply this to previous images as well, then you may want to install the [Regenerate Thumbnails](https://wordpress.org/plugins/regenerate-thumbnails/) plugin on your site so that all images are regenerated using your new settings.
