---
layout: post
title:  "Sanitizing a hex color value in PHP"
permalink: blog/php-sanitize-hex-color
categories:
    - PHP
---

Working with WordPress themes it's not unusual to have to manipulate colors is PHP. This is the first of a series of posts on working with color in WordPress and PHP in general.

One of the most important things is usually making sure that the string we're working with is properly formatted as a hex value.

The function below will do just that:

```php
<?php
/**
 * Sanitises a HEX value.
 * The way this works is by splitting the string in 6 substrings.
 * Each sub-string is individually sanitized, and the result is then returned.
 *
 * This function is part of the `Kirki_Color` class in the [Kirki](http://kirki.org) Toolkit.
 *
 * @var     string      The hex value of a color
 * @param   boolean     Whether we want to include a hash (#) at the beginning or not
 * @return  string      The sanitized hex color.
 */
function aristath_sanitize_hex( $color = '#FFFFFF', $hash = true ) {

    // Remove any spaces and special characters before and after the string
    $color = trim( $color );

    // Remove any trailing '#' symbols from the color value
    $color = str_replace( '#', '', $color );

    // If the string is 6 characters long then use it in pairs.
    if ( 3 == strlen( $color ) ) {
        $color = substr( $color, 0, 1 ) . substr( $color, 0, 1 ) . substr( $color, 1, 1 ) . substr( $color, 1, 1 ) . substr( $color, 2, 1 ) . substr( $color, 2, 1 );
    }

    $substr = array();
    for ( $i = 0; $i <= 5; $i++ ) {
        $default    = ( 0 == $i ) ? 'F' : ( $substr[$i-1] );
        $substr[$i] = substr( $color, $i, 1 );
        $substr[$i] = ( false === $substr[$i] || ! ctype_xdigit( $substr[$i] ) ) ? $default : $substr[$i];
    }
    $hex = implode( '', $substr );

    return ( ! $hash ) ? $hex : '#' . $hex;

}
?>
```

Usage:

```php
<?php

$color = 'F77C90';
$sanitized_color = aristath_sanitize_hex( $color );
?>
```
