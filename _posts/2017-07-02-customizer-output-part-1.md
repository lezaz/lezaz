---
layout: post
title: "Automating CSS Output & postMessage scripts with Kirki (Part 1)"
permalink: "wordpress/customizer/2017/07/02/customizer-output-part-1"
categories:
  - WordPress
  - Customizer
---

### [Read Part 2: Automating CSS Output & postMessage scripts with Kirki](http://aristath.github.io/wordpress/customizer/2017/07/04/customizer-output-part-2.html)
<br><br>
This will be the first of a series of posts on how to use one of my favorite features of the [Kirki Plugin](https://wordpress.org/plugins/kirki): Its ability to automate the CSS output and the generation of `postMessage` scripts for the Wordpress Customizer.

Let's say for example that you want to do the following:
* Create a Section in the customizer
* Add a new color field to that section
* When the color in the control changes, change the background color of the `#content` element in your page
* The above change has to live-update the customizer preview pane without a refresh.

Using the Customizer API you would write something like this:

```php
<?php

// Register the section, setting & control.
add_action( 'customize_register', function( $wp_customize ) {
    
    // Add the section.
    $wp_customize->add_section( 'mytheme_new_section_name' , array(
        'title'    => __( 'Section Name', 'mytheme' ),
        'priority' => 30,
    ) );
    
    // Add Setting.
    $wp_customize->add_setting( 'content_color' , array(
        'default'           => '#FFFFFF',
        'transport'         => 'postMessage',
        'sanitize_callback' => 'aristath_sanitize_hex',
    ) );
    
    // Add Control.
    $wp_customize->add_control( new WP_Customize_Color_Control( $wp_customize, 'content_color', array(
        'label'    => __( 'Content Color', 'mytheme' ),
        'section'  => 'mytheme_new_section_name',
        'settings' => 'content_color',
        'priority' => 10,
    ) ) );
} );

// Enqueue the script for postMessage.
add_action( 'customize_preview_init', function() {
    wp_enqueue_script( 'mytheme-themecustomizer', get_template_directory_uri() . '/assets/js/theme-customizer.js', array( 'jquery','customize-preview' ), false, true )
} );

// Add CSS for the frontend.
// This is NOT how you'd do it in a normal theme.
// You'd ideally add this using "wp_add_inline_style".
add_action( 'wp_head', function() {
    $content_bg_color = get_theme_mod( 'header_color', '#000000' );
    ?>
    <style type="text/css">
        #content { background-color: <?php echo esc_attr( $content_bg_color ); ?>; }
    </style>
    <?php
} );

// Sanitization function for HEX colors.
function aristath_sanitize_hex( $color = '#FFFFFF' ) {
    $color = str_replace( '#', '', trim( $color ) );
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
    return '#' . $hex;
}
```
Then you'd write JS similar to this in your `/assets/js/theme-customizer.js` file inside your theme:

```js
( function() {
    
    // Update site title color in real time.
	wp.customize( 'content_color', function( value ) {
		value.bind( function( newval ) {
			jQuery( '#content' ).css( 'background-color', newval );
		} );
	} );
} )( jQuery );
```

Using Kirki however, we can simplify all the above and write this in a PHP file:

```php
<?php

// Add our config to differentiate from other themes/plugins 
// that may use Kirki at the same time.
Kirki::add_config( 'mytheme', array(
	'capability'  => 'edit_theme_options',
	'option_type' => 'theme_mod',
) );

// Add Section.
Kirki::add_section( 'mytheme_new_section_name', array(
	'title'    => __( 'Section Name', 'mytheme' ),
	'priority' => 30,
) );

// Add Field (setting & control defined as one).
Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'content_color',
	'label'     => __( 'Content Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#FFFFFF',
	'priority'  => 10,
	'transport' => 'auto',
	'output'    => array(
		array(
			'element'  => '#content',
			'property' => 'background-color'
		),
	),
) );
```

The above 2 methods have the exact same result, but the 2nd method is easier to understand and you don't need to write any custom sanitization functions (they are automatically applied), you don't have to write a custom function to output your CSS, and finally you don't have to write any custom JS either. The combination of the `output` argument with `'transport' => 'auto'` takes care of the JS generation for you. Isn't that cool? The `output` argument is defined as an array of arrays so if you want you can make the same color apply to multiple things. For example you can write something like this:

```php
'output' => array(
    array(
        'element'  => '#content',
        'property' => 'background-color',
    ),
    array(
        'element'  => '#sidebar-1',
        'property' => color,
    ),
),
```

The above will make the color you select be applied as a `background-color` to the `#content` element and also as text color in `#sidebar-1`.

Any changes you make to the `output` argument will automatically be applied both to the CSS that's generated for the frontend view of your site as well as the `postMessage` script.

> That's all nice, but writing my own scripts allows me greater versatility.

True, but don't forget that the above is a pretty simple example and I didn't use any of the more advanced features/arguments from Kirki. We'll examine those in future posts in more detail. That being said, if you can spare the time to write your own scripts there's no doubt they will be better since they will be custom-tailored for your own use-case. If you can afford to write them, you should.

I'll close this post with a word of caution:

<div style="background-color:rgba(0,0,0,.05);padding:2rem;">
<p>Even though Kirki simplifies some things and makes life easier, it's very important for developers to start without Kirki. You will have to learn the Customizer API and the way things work if you want to build things properly.</p>
<p>If you think of a theme you're building as a house, then WordPress core and its APIs are the materials you'll be using. Kirki is nothing more than a tool. Learn your materials before you pick up a tool, otherwise you'll end up breaking things.</p>
</div>

<br><br>

### [Read Part 2: Automating CSS Output & postMessage scripts with Kirki](http://aristath.github.io/wordpress/customizer/2017/07/04/customizer-output-part-2.html)
