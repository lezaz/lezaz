---
layout: post
title:  "Kirki_Color: A PHP Color Calculations class"
permalink: blog/php-color-manipulation-class
categories:
    - PHP
---

Yesterday I started working on a new series of posts about manipulating color in WordPress and PHP in general.

However, I realized that over the past 2 years I've written a lot of functions and classes to work with color, and the best and most complete so far is the one included in the [Kirki](http://kirki.org) Toolkit.

So instead of writing a separate post for each of these methods, I'm going to just write examples of how to use it in your own projects.

Note: You can see the documentation for the Kirki_Color class on its [github wiki](https://github.com/reduxframework/kirki/wiki/Kirki_Color).

You can get the Kirki_Color class from github: [https://github.com/aristath/kirki/blob/master/includes/class-kirki-color.php](https://github.com/aristath/kirki/blob/master/includes/class-kirki-color.php)

## Example 1:

In this example, we're going to suppose that we have a background color and we want to automatically change the text color based on that background so our text is always readable.

```php
<?php

// Get the background color
$background_color = get_theme_mod( 'background_color' );

if ( 125 < Kirki::get_brightness( $background_color ) ) {

    // Our background color is dark, so we need to create a light text color.
    $text_color = Kirki_Color::adjust_brightness( $background_color, 140 );

} else {

    // Our background color is light, so we need to create a dark text color
    $text_color = Kirki_Color::adjust_brightness( $background_color, -140 );

}
```
In the example above what we did was start with a background color and then create a text color based on that background by darkening or lightening it.

An alternative would be to use black or white text color based on our background color:

```php
<?php

$background_color = get_theme_mod( 'background_color' );
$text_color = ( 125 < Kirki_Color::get_brightness( $background_color ) ) ? '#fff' : '#333';

?>
```

## Example 2:

Now this is going to be a bit more involved than the previous example.

Let's say we have a background color and a "primary" color and we want to calculate the text & link colors based on these 2 values.

We'll need to get a color for our text, a color for our links, and a color for the hover on links. There are of course numerous ways to do it, this is just one implementation of many.

```php
<?php

$background_color = get_theme_mod( 'background_color' );
$primary_color    = get_theme_mod( 'primary_color' );

// Is our background color dark?
$is_dark_bg = ( 125 < Kirki_Color::get_brightness( $background_color ) ) ? true : false;

/**
 * Calculte the normal text color.
 * If dark background then use white text, else dark text.
 */
$text_color = ( $is_dark_bg ) ? '#fff' : '#222';

/**
 * Calculate the normal link color
 */
// Find the brightness difference between the background and the primary color.
$primary_bg_difference = Kirki_Color::brightness_difference( $background_color, $primary_color );

if ( 125 < $primary_bg_difference ) {

    // If the difference is > 125, then links can be set to the primary color.
    $link_color = $primary_color;

} else {

    /**
     * If the background is dark, then lighten the primary color and use as link color.
     * If the background is light, then darken the primary color and use as link color.
     */
    if ( $is_dark_bg ) {
        $link_color = Kirki_Color::adjust_brightness( $primary_color, 40 );
    } else {
        $link_color = Kirki_Color::adjust_brightness( $primary_color, 40 );
    }

}

/**
 * Calculate the link-hover color.
 */
if ( $is_dark_bg ) {
    // We're on a dark background.
    if ( 0 == Kirki_Color::brightness_difference( '#000', $link_color ) ) {
        // If the link color is black, then lighten it.
        $link_color_hover = Kirki_Color::adjust_brightness( $link_color, 10 );
    } else {
        $link_color_hover = Kirki_Color::adjust_brightness( $link_color, -10 );
    }
} else {
    // We're on a light background.
    if ( 0 == Kirki_Color::brightness_difference( '#fff', $link_color ) ) {
        // If the link color is white, then darken it.
        $link_color_hover = Kirki_Color::adjust_brightness( $link_color, -10 );
    } else {
        $link_color_hover = Kirki_Color::adjust_brightness( $link_color, 10 );
    }
}

?>

<?php
// GENERATE THE CSS
?>
<style>
body {
    color: <?php echo $text_color; ?>;
}
a {
    color: <?php echo $link_color; ?>;
}
a:hover {
    color: <?php echo $link_color_hover; ?>;
}
</style>
```

That's just an example use and I only used calculations based on the color brightness above.
There are other more effecient ways you can use to get the right text color for your background based on luminosity and you can do all that using the methods included in the Kirki_Color class. It's all a matter of how much you can automate the process and how much you'll put your imagination to use. :)
