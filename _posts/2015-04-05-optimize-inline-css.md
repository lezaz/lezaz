---
layout: post
title:  "Optimizing dynamically-generated CSS in WordPress"
categories:
    - CSS
    - WordPress
---
When building a WordPress theme (or plugin) we sometimes need to generate some styles based on the user options, and then add these styles to the `<head>`.

Usually theme developers accomplish this by doing something like this:

```php
<?php

function my_dynamic_styles() { ?>

    <?php if ( 1 == get_option( 'option_1', 'default' ) ) : ?>
        <style>body { color: #333; }</style>
    <?php endif; ?>

    <?php if ( 1 == get_option( 'option_2', 'default' ) ) : ?>
        <style>body { background: #fff; }</style>
    <?php endif; ?>

    <style>body a { color: <?php echo get_option( 'option_3', 'default' ); ?> }</style>
    <?php endif; ?>
    <?php

}
add_action( 'wp_head', 'my_dynamic_styles', 99 );
```
OK, maybe not exactly like that, but you get the point.
What we usually see is themes echoing styles to the head using a similar method: We check the value of a setting and depending on that value we echo the corresponding CSS.

This may work well for small projects where the CSS we add is no more than a dozen lines.

However, if start evolving that project and start adding more options or more complicated CSS, then this becomes unmanageable. It's not rare to see a value set at some point in our CSS, only to be overriden just a few lines below because of some other code.

The below methos is a way of handling these issues a bit more efficiently:

```php
<?php

function my_dynamic_styles() {

    $styles = array();

    if ( 1 == get_option( 'option_1', 'default' ) ) {
        $css['body']['color'] = '#333';
    }

    if ( 1 == get_option( 'option_2', 'default' ) ) {
        $css['body']['background'] = '#fff';
    }

    $css['body a']['color'] = get_option( 'option_3', 'default' );

    $css = apply_filters( 'my_custom_css_array_filter', $css );

    $final_css = '';
    foreach ( $css as $style => $style_array ) {
        $final_css .= $style . '{';
        foreach ( $style_array as $property => $value ) {
            $final_css .= $property . ':' . $value . ';';
        }
        $final_css .= '}';
    }

    echo $final_css;
}
add_action( 'wp_head', 'my_dynamic_styles', 99 );
```

What we're doing above is more flexible.  
We're creating an array of CSS elements with their properies and their values.
Imagine that the array in the above example would look like this:

```php
$css = array(
    'body' => array(
        'color'      => '#333';
        'background' => '#fff';
    ),
    'body a' => array(
        'color'      => get_option( 'option_3', 'default' ),
    ),
);
```

It may look simple here, but it can grow pretty big and the bigger it gets, the more performant it is compared to the first method that developers use thus far.

Then we also have the flexibility of further manipulating that array using the `'my_custom_css_array_filter'` filter that I used above, and as an extra advantage, the CSS that is then parsed from the `foreach` function is minimized and there are no duplicate values in your CSS.
