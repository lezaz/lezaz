---
layout: post
title:  "Improvements coming to the next version of Kirki"
permalink: blog/improvements-kirki-version-1.0.0
categories: WordPress
---

Kirki started a couple years ago as a way to simplify the use of the customizer and make it easier for people to add custom controls to their themes without having to reinvent the wheel every time they needed to.

So far we've been on Kirki versions 0.x.x because everything was still in a state of flux and we were changing things around, experimenting with different syntaxes, structures etc.  
Finally now we're about to release version 1.0.0 of the plugin, the first stable version that will be complete. The reason is that recently a lot of improvements have been made to the WordPress Customizer and things that we could not easily accomplish earlier are now possible and almost ready.

### Change of concept

The first major change is the concept: Kirki started as a _framework_ and now it's turning more into a _toolkit_.
You can use Kirki to create Customizer panels, sections and controls, or you can use the standard [WordPress Customizer API](https://codex.wordpress.org/Theme_Customization_API) and just use the features that you want, disregarding all the rest.

### PHP 5.2 Compatibility

This one is not something that I wanted to do... To be honest I personally despise PHP 5.2 and Kirki was a lot better without it since we were using namespaces and a lot of features that are simply not available in a 10-year-old, insecure and antiquated version of PHP.  
However, I understand that the minimum requirement for WordPress is PHP 5.2 so after popular demand (mostly in the TRT channel in Slack) **we made our code worse** in order to be compatible with antiquated hosts and their configurations.  
Hopefully WordPress core will soon have PHP 5.3 as a minimum requirement and we'll be able to improve our code again.

### Independent controls

In case you just want to use a specific control we have, we've made it easier for you to hack and slash it: All controls are now self-contained and each one of them lives in a separate folder inside the [`includes/controls`](https://github.com/aristath/kirki/tree/master/includes/controls) folder. In that folder you'll find the PHP class, any `.js` files that are needed and if the field requires some custom CSS then you'll also find an accompanying `.css` and `.scss` file for that field.

This way you can just grab the files that interest you and use them in your own projects.

### 3 ways to add your fields.

We understand that each project is different and every developer likes working in a different way. To that end we now give you the choice of choosing the syntax you want to use:

1. The default WordPress Customizer API.
2. An array of arrays
3. Static method calls

The default WordPress Customizer syntax is one of the most versatile ways to use the Customizer. You should already be familiar with it and if you're not then we strongly advise you to read [this blog post from Otto](http://ottopress.com/2012/how-to-leverage-the-theme-customizer-in-your-own-themes/) to learn the basics of how to use the WordPress customizer and then the Customizer Handbook on [[developer.wordpress.org](https://developer.wordpress.org/themes/advanced-topics/customizer-api/) before you decide you want to work with Kirki.

Example: adding a few fields using the WordPress Customizer API:

```php
<?php

function my_custom_text_settings( $wp_customize ) {

	// Register the settings
	$wp_customize->add_setting( 'my_setting', array(
		'default'        => 'some-default-value',
		'type'           => 'theme_mod',
		'capability'     => 'edit_theme_options',
	) );

	$wp_customize->add_setting( 'my_setting_2', array(
		'default'        => 'some-default-value',
		'type'           => 'theme_mod',
		'capability'     => 'edit_theme_options',
	) );

	// Add the controls
	$wp_customize->add_control( 'my_setting', array(
		'label'       => __( 'My custom control', 'translation_domain' ),
		'section'     => 'my_section',
		'settings'    => 'my_setting',
		'type'        => 'text',
		'priority'    => 10
    ) );

	$wp_customize->add_control( 'my_setting_2', array(
		'label'       => __( 'My custom control 2', 'translation_domain' ),
		'section'     => 'my_section',
		'settings'    => 'my_setting_2',
		'type'        => 'text',
		'priority'    => 20
    ) );
}
add_action( 'customize_register', 'my_custom_text_settings' );
?>
```

===========================================

For some projects you may find it convenient to write your fields in the form of an array of arrays. This array can then be hooked in the `kirki/fields` filter and your fields will be automatically added to the customizer. This syntax is simpler than the default Customizer API for most people and still allows you to make a lot of customizations.

Example: adding the same 2 fields using the `kirki/fields` filter:

```php
<?php

function my_custom_text_settings( $fields ) {

	// Add the controls
	$fields[] = array(
		'label'       => __( 'My custom control', 'translation_domain' ),
		'section'     => 'my_section',
		'settings'    => 'my_setting',
		'type'        => 'text',
		'priority'    => 10,
		'options_type' => 'theme_mod',
		'capability'   => 'edit_theme_options',
	);

	$fields[] = array(
		'label'       => __( 'My custom control 2', 'translation_domain' ),
		'section'     => 'my_section',
		'settings'    => 'my_setting_2',
		'type'        => 'text',
		'priority'    => 20,
		'options_type' => 'theme_mod',
		'capability'   => 'edit_theme_options',
	);

	return $fields;

}
add_filter( 'kirki/fields', 'my_custom_text_settings' );
?>
```

===========================================

In other projects you may want to add your fields using static method calls. This is the method that we find offers great flexibility and potential without sacrificing the flexibility of the system:

```php
<?php

Kirki::add_config( 'my_config', array(
	'options_type' => 'theme_mod',
	'capability'   => 'edit_theme_options',
) );

Kirki::add_field( 'my_config', array(
	'settings' => 'my_setting',
	'label'    => __( 'My custom control', 'translation_domain' ),
	'section'  => 'my_section',
	'type'     => 'text',
	'priority' => 10,
	'default'  => 'some-default-value',
) );

Kirki::add_field( 'my_config', array(
	'settings' => 'my_setting_2',
	'label'    => __( 'My custom control 2', 'translation_domain' ),
	'section'  => 'my_section',
	'type'     => 'text',
	'priority' => 20,
	'default'  => 'some-default-value',
) );
?>
```

===========================================

Please keep in mind that if you do work using the default WordPress customizer API, then you will not be able to take advantage of some of Kirki's more advanced features like automatic CSS output & calculations as well as automatic `postMessage` script generation.
These features will only be available if you use the 2nd or 3rd method of adding your own fields.

### Custom Automatic CSS

If you use the default WordPress Customizer API syntax and still want to take advantage of our automatic CSS generation then you can do so using the newly-introduced `Kirki_Output` class.

Example:

```php
<?php

$css = Kirki_Output::css( 'my_setting', 'theme_mod', array(
    array(
        'element'  => 'a',
        'property' => 'color',
    ),
    array(
        'element'  => 'a:hover',
        'property' => 'border-bottom-color',
        'suffix'   => ' ! important',
    )
) );
?>
```
The above code for example will take the value of a `theme_mod` with an id of `my_setting` and generate the css needed to apply the value of that setting to links, and also change the `border-bottom-color` on hover for the links.

You can then enqueue that CSS however you wish (we recommend using the[`wp_add_inline_style`](https://codex.wordpress.org/Function_Reference/wp_add_inline_style) function to do that), [write it to file](http://aristeides.com/blog/avoid-dynamic-css-in-head/) or do whatever you need to do with that CSS.

### Automatic generation of `active_callback` functions

If you want to show or hide your controls based on the values of other controls you can use the `required` argument for your fields. More information on that can be found in our [documentation](https://github.com/reduxframework/kirki/wiki/required).

### Redux Compatibility

This is still a work in progress and I'm not sure it will be included in version 1.0.0, but the concept is this:

If you add this line in your code somewhere:

```php
<?php define( 'KIRKI_REDUX_COMPATIBILITY', true ); ?>
```
and uninstall Redux, then Kirki will automatically detect your Redux configuration and use your already existing fields in the customizer. No custom coding required or anything else, it will automatically be taken care of for you.

As I mentioned above this is still a work in progress and not a top priority for version 1.0.0, but if you're using Redux and would like to see this feature completed then we'd appreciate all the help you can give us. You can find the class responsible for parsing the Redux fields and converting them customizer controls using the Kirki API here: [https://github.com/reduxframework/kirki/blob/master/includes/redux-compatibility.php](https://github.com/reduxframework/kirki/blob/master/includes/redux-compatibility.php)

### Conclusion

To conclude this is the largest update by far until now and we feel it will help a lot of WordPress Theme developers use the WordPress Customizer more efficiently.

We still have a few bugs to fix and would love your help in order to fix them all and release this sooner. We feel it's a big step forward and will help a lot of people. If you want to help out you can visit the [issues queue in our github repository](https://github.com/aristath/kirki/issues) and do your best! :)
