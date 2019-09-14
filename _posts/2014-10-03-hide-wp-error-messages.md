---
layout: post
title:  "How to hide PHP Warnings and Notices in WordPress"
permalink: blog/wp-hide-php-errors
categories:
    - WordPress
---

From time to time a user comes to me and says _"I see some PHP notices and warnings on my page"_.  

Most of the time these are nothing to worry about (though the plugin/theme developer should know about these so that they may fix them in a future release).
PHP warnings and notices are nothing to worry about on a production site most of the time.  
Some of these can even be generated because the developer has to keep compatibility with older versions of WordPress as well as older PHP versions.

### The solution:

If you simply set `WP_DEBUG` to `false` in your `wp-config.php` file you should be fine. **These don't affect your site in any way**.

However, the problem is that some times the above does not work.  
That can happen most times on cheap shared hosts that force displaying PHP warnings and notices.  
In that case, you can replace this line from your wp-config.php file:

```php
define('WP_DEBUG', false);
```

with this:

```php
ini_set('display_errors','Off');
ini_set('error_reporting', E_ALL );
define('WP_DEBUG', false);
define('WP_DEBUG_DISPLAY', false);
```

I hope that helps someone out there!
