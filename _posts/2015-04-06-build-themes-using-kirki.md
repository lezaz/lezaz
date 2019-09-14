---
layout: post
title:  "Building WordPress themes using the Kirki Customizer"
permalink: blog/build-wordpress-theme-using-kirki
categories:
    - Customizer
    - WordPress
---

Building your own WordPress theme can be daunting and pretty complicated.
Usually the process of building a WordPress theme follows these steps:

1. Find a concept for the theme
2. Design the theme
3. Start coding the markup/structure
4. Write the CSS
5. Add some options for the user so they can further personalize it.

In this article I'm not going to deal with the first 4 steps, I'm only going to focus on the 5th step: adding options for users.

There a re many frameworks out there that allow you to create an admin page on WordPress, add options there and allow your users to change some things on your theme. And then there's the WordPress customizer, a native WordPress solution that allows you to add options in a way that users can make changes to their options and live-preview their site before saving their selections.

[Kirki](http://kirki.org) lies somewhere in the middle: It's a framework that allows you to add advanced options to the WordPress customizer (without any admin pages) and provides some advanced features for you. It will allow you to add your own custom fields to the customizer a lot faster and more efficiently!

So let's say you created the basics of your theme and now you want to add some options.

You've read about Kirki and you've seen its documentation and you're about to get started. The first thing you have to decide is how you're going to use Kirki.

You have 2 options:

1. Use as a plugin
2. Embed in your theme


## Use as a plugin

If you want to use Kirki as a plugin you can require it using a library like [TGM Plugin Acivation](http://tgmpluginactivation.com/). The Kirki plugin is available on [wordpress.org](https://wordpress.org/plugins/kirki/) so including it is really easy


## Embed in your theme

If you want to bundle Kirki in your theme you can do it pretty easily following these simple instructions.

### Step 1: Download Kirki and place it in your theme.

You can download the plugin from [wordpress.org](https://wordpress.org/plugins/kirki/) and after you extract it put it inside your theme, let's ssay in a folder called `includes/kirki`.

### Step 2: Include the main plugin file in your theme's `functions.php` file.

That's pretty simple... Just add this line in your theme's `functions.php` file:

```php
<?php include_once( dirname( __FILE__ ) . '/includes/kirki/kirki.php' ); ?>
```

### Step 3: Configure Kirki to use the proper URL path

Kirki loads some files when in the customizer and therefore needs you to tell it exactly where these files are located. You will need to add the following in your theme's `functions.php`:

```php
<?php
function mytheme_kirki_configuration() {
    return array( 'url_path'     => get_stylesheet_directory_uri() . '/includes/kirki/' );
}
add_filter( 'kirki/config', 'mytheme_kirki_configuration' );
?>
```

Of course if you want you can add more configuration options and further customize the way your theme's customizer will look etc, but the only thing we'll need for our basic theme is the above.

### Step 4: Add your fields

You can now start adding your fields and structuring your options.

For my theme I want to have the following:

* A "Backgrounds" panel with 3 sections inside it:
    1. Header Background
    2. Body Background
    3. Footer Background
* A typography section where users will be able to choose the font-family for their body and the font-size. I could add more, but this is just an example to showcase what you can do

First of all we'll have to create the Customizer panels and sections:

```php
<?php

function mytheme_kirki_sections( $wp_customize ) {
	/**
	 * Add panels
	 */
	$wp_customize->add_panel( 'backgrounds', array(
		'priority'    => 10,
		'title'       => __( 'Backgrounds', 'kirki' ),
	) );

	/**
	 * Add sections
	 */
     $wp_customize->add_section( 'header_background', array(
 		'title'       => __( 'Header Background', 'kirki' ),
 		'priority'    => 10,
 		'panel'       => 'backgrounds',
 	) );

     $wp_customize->add_section( 'main_background', array(
 		'title'       => __( 'Main Background', 'kirki' ),
 		'priority'    => 20,
 		'panel'       => 'backgrounds',
 	) );

     $wp_customize->add_section( 'footer_background', array(
 		'title'       => __( 'Footer Background', 'kirki' ),
 		'priority'    => 30,
 		'panel'       => 'backgrounds',
 	) );

     $wp_customize->add_section( 'typography', array(
 		'title'       => __( 'Typography', 'kirki' ),
 		'priority'    => 20,
 	) );

}
add_action( 'customize_register', 'mytheme_kirki_sections' );

?>
```

We're not doing anything special here, we're just adding sections and panels following the [WordPress Codex instructions](https://codex.wordpress.org/Theme_Customization_API#Adding_a_New_Section).

Now that we added our sections, we can move on to adding our fields.

All our settings will have to be put inside a function that will then be hooked to the `kirki/fields` filter like this:

```php
<?php

function mytheme_kirki_fields( $wp_customize ) {
    // Add our fields here
}
add_filter( 'kirki/fields', 'mytheme_kirki_fields' );

?>
```

Let's add the header background first.
I want my users to be able to select a background color, an opacity for that color, a background image, whether or not that image has to be repeated, the position of the background image, its size, and finally its attach property.

My field will look like this:

```php
$fields[] = array(
    'type'        => 'background',
    'settings'    => 'header_background',
    'label'       => __( 'Choose your header background', 'kirki' ),
    'description' => __( 'The header background you specify here will apply to the header area, including your menus and your branding.', 'kirki' ),
    'section'     => 'header_background',
    'default'     => array(
        'color'    => 'rgba(25,170,141,0.7)',
        'image'    => '',
        'repeat'   => 'no-repeat',
        'size'     => 'cover',
        'attach'   => 'fixed',
        'position' => 'left-top',
    ),
    'priority'    => 10,
);
```

That will create the setting in the database and add all necessary controls in the customizer.
But what about using these options to actually style the background of the header? I could write my own custom functions to get the values of all those settings, do some math, sanitize everything and then apply the appropriate styling to my theme.

However Kirki can automatically do all of the above using the `output` argument. So if I want to apply those styles to a `div` element on my page that has an ID of `header` I'd add an extra line to my header field:

```php
$fields[] = array(
    'type'        => 'background',
    'settings'    => 'header_background',
    'label'       => __( 'Choose your header background', 'kirki' ),
    'description' => __( 'The header background you specify here will apply to the header area, including your menus and your branding.', 'kirki' ),
    'section'     => 'header_background',
    'default'     => array(
        'color'    => 'rgba(25,170,141,0.7)',
        'image'    => '',
        'repeat'   => 'no-repeat',
        'size'     => 'cover',
        'attach'   => 'fixed',
        'position' => 'left-top',
    ),
    'priority'    => 10,
    'output'      => '#header'
);
```

Next up: The body background.
I'll just duplicate the above and change the `settings` argument and the description:

```php
$fields[] = array(
    'type'        => 'background',
    'settings'    => 'body_background',
    'label'       => __( 'Choose the background for the main area', 'kirki' ),
    'description' => __( 'The header background you specify here will apply to the main area of your site.', 'kirki' ),
    'section'     => 'main_background',
    'default'     => array(
        'color'    => 'rgba(255,255,255,1)',
        'image'    => '',
        'repeat'   => 'no-repeat',
        'size'     => 'cover',
        'attach'   => 'fixed',
        'position' => 'left-top',
    ),
    'priority'    => 10,
    'output'      => '#content'
);
```

Next is the footer background.

This time I don't want my users to be able to choose the background repeat, its size and position 'cause I've hard-coded those in my CSS. So I'll just skip them and not include them at all in my new field:

```php
$fields[] = array(
    'type'        => 'background',
    'settings'    => 'footer_background',
    'label'       => __( 'Choose the background for your footer', 'kirki' ),
    'description' => __( 'If you choose to use an image then please use a blurry image so that it does not distract users from the widgets you have on your footer..', 'kirki' ),
    'section'     => 'footer_background',
    'default'     => array(
        'color'    => 'rgba(255,255,255,1)',
        'image'    => '',
        'attach'   => 'fixed',
    ),
    'priority'    => 10,
    'output'      => '#content'
);
```

And I'm done with my backgrounds! Moving on to my typography settings now.

Let's start with the font-family:

I want to have a dropdown that will allow users to choose the font they want. I'll use Kirki to get a list of all the google-font families, automatically generate the script required for those, automatically have it added to my page, it will automatically generate the CSS for it, apply it, AND also take this a step further, taking advantage of WordPress's [`postMessage`](https://codex.wordpress.org/Theme_Customization_API#Part_3:_Configure_Live_Preview_.28Optional.29) so that previews are instant.

```php
$fields[] = array(
    'type'        => 'select',
    'setting'     => 'font_family',
    'label'       => __( 'Font-Family', 'kirki' ),
    'description' => __( 'Please choose a font for your site. This font-family will be applied to all elements on your page, including headers and body.', 'kirki' ),
    'section'     => 'typography',
    'default'     => 'Roboto',
    'priority'    => 10,
    'choices'     => Kirki_Fonts::get_font_choices(),
    'output'      => array(
        array(
            'element'  => 'body, h1, h2, h3, h4, h5, h6',
            'property' => 'font-family',
        ),
    ),
    'transport'   => 'postMessage',
    'js_vars'     => array(
        array(
            'element'  => 'body, h1, h2, h3, h4, h5, h6',
            'function' => 'css',
            'property' => 'font-family',
        ),
    ),
);
```

And now for the font-size:
I want it to be a slider control, and I want my users to use `em` as the unit for their font-size instead of `px`. The minimum value for the font-size will be 0.7, the maximum value will be 2, and I want the step to be pretty fluid, so I'll set that to 0.01. I don't want the CSS to be applied to headers, just the body element since headers will be calculated based on that value instead by the browser automatically. So this will be the field:

```php
$fields[] = array(
    'type'        => 'slider',
    'setting'     => 'font_size',
    'label'       => __( 'Font-Size', 'kirki' ),
    'description' => __( 'Please choose a font-size for your body.', 'kirki' ),
    'section'     => 'typography',
    'default'     => 1,
    'priority'    => 20,
    'choices'     => array(
        'min'  => .7,
        'max'  => 2,
        'step' => .01
    ),
    output        => array(
        array(
            'element'  => 'body',
            'property' => 'font-size',
            'units'    => 'em',
        ),
    ),
    'transport'   => 'postMessage',
    'js_vars'     => array(
        array(
            'element'  => 'body',
            'function' => 'css',
            'property' => 'font-size',
        ),
    ),
);
```

Our final function containing the definitions for our fields should now look like this:

```php
<?php

function mytheme_kirki_fields( $fields ) {

    $fields[] = array(
        'type'        => 'background',
        'settings'    => 'header_background',
        'label'       => __( 'Choose your header background', 'kirki' ),
        'description' => __( 'The header background you specify here will apply to the header area, including your menus and your branding.', 'kirki' ),
        'section'     => 'header_background',
        'default'     => array(
            'color'    => 'rgba(25,170,141,0.7)',
            'image'    => '',
            'repeat'   => 'no-repeat',
            'size'     => 'cover',
            'attach'   => 'fixed',
            'position' => 'left-top',
        ),
        'priority'    => 10,
        'output'      => '#header'
    );

    $fields[] = array(
        'type'        => 'background',
        'settings'    => 'body_background',
        'label'       => __( 'Choose the background for the main area', 'kirki' ),
        'description' => __( 'The header background you specify here will apply to the main area of your site.', 'kirki' ),
        'section'     => 'main_background',
        'default'     => array(
            'color'    => 'rgba(255,255,255,1)',
            'image'    => '',
            'repeat'   => 'no-repeat',
            'size'     => 'cover',
            'attach'   => 'fixed',
            'position' => 'left-top',
        ),
        'priority'    => 10,
        'output'      => '#content'
    );

    $fields[] = array(
        'type'        => 'background',
        'settings'    => 'footer_background',
        'label'       => __( 'Choose the background for your footer', 'kirki' ),
        'description' => __( 'If you choose to use an image then please use a blurry image so that it does not distract users from the widgets you have on your footer..', 'kirki' ),
        'section'     => 'footer_background',
        'default'     => array(
            'color'    => 'rgba(255,255,255,1)',
            'image'    => '',
            'attach'   => 'fixed',
        ),
        'priority'    => 10,
        'output'      => '#content'
    );

    $fields[] = array(
        'type'        => 'select',
        'setting'     => 'font_family',
        'label'       => __( 'Font-Family', 'kirki' ),
        'description' => __( 'Please choose a font for your site. This font-family will be applied to all elements on your page, including headers and body.', 'kirki' ),
        'section'     => 'typography',
        'default'     => 'Roboto',
        'priority'    => 10,
        'choices'     => Kirki_Fonts::get_font_choices(),
        'output'      => array(
            array(
                'element'  => 'body, h1, h2, h3, h4, h5, h6',
                'property' => 'font-family',
            ),
        ),
        'transport'   => 'postMessage',
        'js_vars'     => array(
            array(
                'element'  => 'body, h1, h2, h3, h4, h5, h6',
                'function' => 'css',
                'property' => 'font-family',
            ),
        ),
    );

    $fields[] = array(
        'type'        => 'slider',
        'setting'     => 'font_size',
        'label'       => __( 'Font-Size', 'kirki' ),
        'description' => __( 'Please choose a font-size for your body.', 'kirki' ),
        'section'     => 'typography',
        'default'     => 1,
        'priority'    => 20,
        'choices'     => array(
            'min'  => .7,
            'max'  => 2,
            'step' => .01
        ),
        output        => array(
            array(
                'element'  => 'body',
                'property' => 'font-size',
                'units'    => 'em',
            ),
        ),
        'transport'   => 'postMessage',
        'js_vars'     => array(
            array(
                'element'  => 'body',
                'function' => 'css',
                'property' => 'font-size',
            ),
        ),
    );

    return $fields;

}
add_filter( 'kirki/fields', 'mytheme_kirki_fields' );

?>
```

It may not look like much, but what we've accomplished above would probably require a couple hundred more lines to do if we were not using Kirki:

* We'd have to create the theme_mod settings before adding the controls
* We'd have to create our own custom color control for the customizer that would be able to handle rgba values and opacities
* We'd have to create our own custom slider control for the customizer that would be able to handle the font-sizes, or we could use a text control for it but that wouldn't be as practical or pretty
* We'd have to write functions to properly calculate the background values and apply them to all 3 areas of our site
* We'd have to write the JS needed for postMessage to work
* We'd have to write our own custom implementation for Google fonts, not to mention all that includes (calculating the enqueued fonts, adding the CSS etc)

Instead of doing all that, we just created an array specifying what we want and it's all been taken care of. :)

The above is just a basic example of course and there's a lot more you can do! To learn more about Kirki you can take a look at its documentation site on [kirki.org](http://kirki.org), and contribute to its development on its [github repository](https://github.com/aristath/kirki).
