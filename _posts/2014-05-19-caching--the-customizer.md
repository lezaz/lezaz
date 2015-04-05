---
layout: post
title:  "Caching the WordPress Customizer"
permalink: blog/caching-customizer-css
categories: Customizer
author_name : Aristeides Stathopoulos
author_url : /about
author_avatar: aristath
show_avatar : false
feature_image: unsplash-kitsune-4.jpg
show_related_posts: false
---

A lot of themes use the WordPress customizer to generate additional CSS.
That CSS is then usually simply echoed to the `<head>` of the page.

But what happens when you have complicated calculations and/or a lot of styles you want to add? Usually that means that your webserver has to re-calculate everything on each page load.

That might be fine for some themes where for example all you have is the background color, but if you're like me and you're building something like the [Shoestrap](https://github.com/wpmu/shoestrap) theme, then you need something a little more powerful than that.

### Caching to the resque.

Thanfully, WordPress has a lot of caching mechanisms.
**[EDIT: We were previously using the [Transients API](http://codex.wordpress.org/Transients_API). However, following a comment by Otto I realized that transients are not autoloaded, while theme mods are. So instead of using the transients API, we're now simply saving the resulting CSS as another *theme_mod*.]**

Usually in order to get a setting and echo it back to your `<head>` you'd do something like this:

```php
<?php

/**
 * Apply our custom background color to the <body> of the document.
 */
function my_custom_styles() {

	// Get the background color from the db
	$bg = get_theme_mod( 'background_color' );

	// Build the styles
	$style = 'body { background: ' . $bg . '; }';

	// Add the CSS inline.
	// Please note that you must first enqueue the actual 'my-styles' stylesheet.
	// See http://codex.wordpress.org/Function_Reference/wp_add_inline_style#Examples
	wp_add_inline_style( 'my-styles', $style );

}
add_action( 'wp_enqueue_scripts', 'my_custom_styles', 130 );

?>
```

What we'll do here, is introduce a proxy function.
The purpose of this will be to cache the resulting CSS in a separate theme_mod so that we don't have to re-calculate it each and every time:

```php
<?php

/**
 * Cache the customizer styles
 */
function my_customizer_styles_cache() {
	global $wp_customize;

	// Check we're not on the Customizer.
	// If we're on the customizer then DO NOT cache the results.
	if ( ! isset( $wp_customize ) ) {

		// Get the theme_mod from the database
		$data = get_theme_mod( 'my_customizer_styles', false );

		// If the theme_mod does not exist, then create it.
		if ( $data == false ) {
			// We'll be adding our actual CSS using a filter
			$data = apply_filters( 'my_styles_filter', null );
			// Set the theme_mod.
			set_theme_mod( 'my_customizer_styles', $data );
		}

	// If we're on the customizer, get all the styles using our filter
	} else {

		$data = apply_filters( 'my_styles_filter', null );

	}

	// Add the CSS inline.
	// Please note that you must first enqueue the actual 'my-styles' stylesheet.
	// See http://codex.wordpress.org/Function_Reference/wp_add_inline_style#Examples
	wp_add_inline_style( 'my-styles', $data );

}
add_action( 'wp_enqueue_scripts', 'my_customizer_styles_cache', 130 );

?>
```
The above function will get the styles using a custom filter `my_styles_filter` and then saves it in the `my_customizer_styles` theme_mod.
We've also added a check so that when you're in the customizer, you will not get the cached CSS but the live CSS instead.

The only problem with that implementation is that when you save your changes to the customizer, you will not see the changes go live since the previous CSS is already cached.

In order to overcome this we have to write another function. One that will simply delete the existing theme_mod when you hit the *save* button on your customizer:

```php
<?php

/**
 * Reset the cache when saving the customizer
 */
function my_reset_style_cache_on_customizer_save() {

	remove_theme_mod( 'my_customizer_styles' );

}
add_action( 'customize_save_after', 'my_reset_style_cache_on_customizer_save' );

?>
```

Now let's put all of the above together and we're done!

```php
<?php

/**
 * Apply our custom background color to the <body> of the document.
 */
function my_custom_styles() {

	// Get the background color from the db
	$bg = get_theme_mod( 'background_color' );

	// Build the styles
	$style = 'body { background: ' . $bg . '; }';

	// Add the CSS inline.
	// Please note that you must first enqueue the actual 'my-styles' stylesheet.
	// See http://codex.wordpress.org/Function_Reference/wp_add_inline_style#Examples
	wp_add_inline_style( 'my-styles', $style );

}
add_action( 'wp_enqueue_scripts', 'my_custom_styles', 130 );

/**
 * Cache the customizer styles
 */
function my_customizer_styles_cache() {
	global $wp_customize;

	// Check we're not on the Customizer.
	// If we're on the customizer then DO NOT cache the results.
	if ( ! isset( $wp_customize ) ) {

		// Get the theme_mod from the database
		$data = get_theme_mod( 'my_customizer_styles', false );

		// If the theme_mod does not exist, then create it.
		if ( $data == false ) {
			// We'll be adding our actual CSS using a filter
			$data = apply_filters( 'my_styles_filter', null );
			// Set the theme_mod.
			set_theme_mod( 'my_customizer_styles', $data );
		}

	// If we're on the customizer, get all the styles using our filter
	} else {

		$data = apply_filters( 'my_styles_filter', null );

	}

	// Add the CSS inline.
	// Please note that you must first enqueue the actual 'my-styles' stylesheet.
	// See http://codex.wordpress.org/Function_Reference/wp_add_inline_style#Examples
	wp_add_inline_style( 'my-styles', $data );

}
add_action( 'wp_enqueue_scripts', 'my_customizer_styles_cache', 130 );

/**
 * Reset the cache when saving the customizer
 */
function my_reset_style_cache_on_customizer_save() {

	remove_theme_mod( 'my_customizer_styles' );

}
add_action( 'customize_save_after', 'my_reset_style_cache_on_customizer_save' );

?>
```

This is how we'll be implementing the customizer caching on the upcoming version of the Shoestrap theme, so if you want to take a look at a somewhat more complex implementation, feel free to [fork the theme on github](https://github.com/wpmu/shoestrap)! **[WARNING: this theme is still experimental and under heavy construction. Not everything is functional.]**
