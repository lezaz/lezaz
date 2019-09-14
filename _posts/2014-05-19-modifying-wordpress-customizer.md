---
layout: post
title:  "Modifying the WordPress Customizer"
permalink: blog/modifying-wordpress-customizer
categories:
    - Customizer
    - WordPress
---

The WordPress Customizer was first introduced in WordPress 3.7 and provides an easy way for themers and plugins developers to allow users to change their settings while at the same time they can see the effects of these changes in real-time on a preview screen.

A lot of people however find the WordPress Customizer a bit "dull" and boring when it comes to the way it looks.
Thankfully, WordPress has a lot of actions and hooks you can use to change the way it looks.

Today we'll see how how can easily add some custom CSS to the head of the Customizer page that will allow you to change the way it looks and make it yours.

The easiest way to do this (but not the best way), is to add some inline styles using the `customize_controls_print_styles` WordPress action:

```php
<?php

/**
 * This function adds some styles to the WordPress Customizer
 */
function my_customizer_styles() { ?>
	<style>
		#customize-theme-controls .accordion-section-title {
			background: #333;
		}
	</style>
	<?php

}
add_action( 'customize_controls_print_styles', 'my_customizer_styles', 999 );

?>
```

That is a pretty simple snippet:

* On line 6 we create a function called `my_customizer_styles`
* On lines 7-11 we write our custom CSS
* On line 15 we enqueue our custom style.

The above works fine when we simply want to change a couple of things...
However when we want to add a lot of modifications it's easier to create a separate CSS file and then enqueue it as seen in the example below:

```php
<?php

/**
 * Enqueue the stylesheet.
 */
function my_enqueue_customizer_stylesheet() {

	wp_register_style( 'my-customizer-css', get_template_directory_uri() . 'assets/css/customizer.css', NULL, NULL, 'all' );
	wp_enqueue_style( 'my-customizer-css' );

}
add_action( 'customize_controls_print_styles', 'my_enqueue_customizer_stylesheet' );

?>
```
As you can see, we used the `customize_controls_print_styles` hook again, but this time instead of writing our stylesheets directly in the PHP file, we enqueued an external stylesheet (in my example above, the styles is located in the assets/css/customizer.css inside my theme).

So there you have it...

You can now add your own custom styles and make the customizer look just the way you want it!

If you hate writing custom code, I recently released a plugin that will do this automatically for you.
It's called [Kirki](http://kirki.org) and besides the ability to change the customizer colors, it also allows you to add a custom header image at the top of your customizer sidebar (where the theme info usually is located) and also adds a lot of custom Controls like textareas, sliders, multi-checkboxes and many, many more!

You can read more about it by clicking the banner below.

<a href="http://kirki.org" alt="Kirki Customizer Customizations WordPress Plugin"><img src="/img/kirki-banner.png"></a>
