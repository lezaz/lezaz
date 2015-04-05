---
layout: post
title:  "How to change the wording of WordPress plugins and themes"
permalink: blog/howto-change-plugin-theme-text
categories: general
author_name : Aristeides Stathopoulos
author_url : /about
author_avatar: aristath
show_avatar : false
feature_image: header-bg.jpg
show_related_posts: false
---

One of the most frequent questions I get is _"How do I change the Wording of a plugin and/or theme?"_

The process I describe below is the simplest one I've found so far, so I decided to write it down so that people may find it more easily.

* Step 1: go to your wp-config.php file. You'll see there a line like this:  
	`define('WPLANG', '');`  
	Change that line to this:
	`define('WPLANG', 'en_US');`  
	(That's of course provided that you only want to change a few phrases and not completely translate WordPress to another language).
* Step 2: Install the [Codestyling Localization](https://wordpress.org/plugins/codestyling-localization/) plugin.
* Step 3: Go from your dashboard to **Tools->Localization**.
* Step 4: Pick your plugin. Press on the "Scan Now" button and then add the en_US language.
* Step 5: Edit the language. You can now search for any text string you want and change it to whatever you need.
* Step 6: Once you're finished changing text strings, press the "generate .mo" button on the top.
* Step 7: Install the [CodeStyling Localization Preserver](https://wordpress.org/plugins/codestyling-localization-preserver/) plugin. This will prevent your changes from being lost each time you update a plugin or theme.

I hope that helps!
