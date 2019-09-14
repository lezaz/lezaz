---
layout: post
title: "Automating CSS Output & postMessage scripts with Kirki (Part 2)"
permalink: "wordpress/customizer/2017/07/04/customizer-output-part-2"
categories:
  - WordPress
  - Customizer
---

### [Read Part 1: Automating CSS Output & postMessage scripts with Kirki](http://aristath.github.io/wordpress/customizer/2017/07/02/customizer-output-part-1.html)
<br><br>
In the previous part of this series we saw a comparison of using the WordPress Customizer API and the Kirki shorter syntax to take care of CSS outputs & postMessage scripts generation.

In this part we'll see how you can define more complex css, combine multiple settings in a single css declaration and more.

### Scenario:

We want to achieve gradients on a button. Since we want this example to be a bit complex in order to show how you can use some extra arguments that were introduced in Kirki 3.0, we'll make this a 2-color gradient background, with adjustable location for the top and bottom colors.

### Creating the fields.

First of all, let's create the fields we'll need. We're going to add 2 color controls and 2 sliders. There are other/simpler ways to do the same thing, but for this example this is what we're going to do.

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

// Add Fields.
Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_top',
	'label'     => esc_attr__( 'Top Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#FFFFFF',
	'priority'  => 10,
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_bottom',
	'label'     => esc_attr__( 'Bottom Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#F2F2F2',
	'priority'  => 11,
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'slider',
	'settings'  => 'color_top_position',
	'label'     => esc_attr__( 'Top Color Position', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => 0,
	'priority'  => 12,
	'choices'   => array(
		'min'  => 0,
		'max'  => 100,
		'step' => 1,
	),
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'slider',
	'settings'  => 'color_bottom_position',
	'label'     => esc_attr__( 'Bottom Color Position', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => 0,
	'priority'  => 13,
	'choices'   => array(
		'min'  => 0,
		'max'  => 100,
		'step' => 1,
	),
) );
```

### Figure out the CSS we want to generate.

What we want to achieve is a css like this:

```css
button {
	background: linear-gradient(to bottom, #1e5799 14%,#7db9e8 77%);
}
```
In the above CSS, `#1e5799` is the value for `color_top`, `#7db9e8` is the value of `color_bottom`, `14` is the value of `color_top_position` and finally `77` is the value of `color_bottom_position`.

So the CSS we want would look like this if we replace the dummy values:
```
linear-gradient(to bottom, {$color_top} {$color_top_position}%,{$color_bottom} {$color_bottom_position}%);
```

Kirki allows to use a `value_pattern` argument for outputs. Inside that `value_pattern`, you can use the `$` sign as a placeholder for the value of the control.

So if we write the `color_top` field again including the output argument it will look like this:

```php
<?php

Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_top',
	'label'     => esc_attr__( 'Top Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#FFFFFF',
	'priority'  => 10,
	'output'    => array(
		array(
			'element'       => 'button',
			'property'      => 'background',
			'value_pattern' => 'linear-gradient(to bottom, $ 14%,#7db9e8 77%)',
		),
	),
) );
```

That will work, but it's rather restrictive since it only allows us to change the value of the top color in the gradient.
That's why in version 3.0.0 we added a new `pattern_replace` argument (`array`).

Let's see an example of that:

```php
<?php

Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_top',
	'label'     => esc_attr__( 'Top Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#FFFFFF',
	'priority'  => 10,
	'output'    => array(
		array(
			'element'         => 'button',
			'property'        => 'background',
			'value_pattern'   => 'linear-gradient(to bottom, $ topPos%,bottomCol bottomPos%)',
			'pattern_replace' => array(
				'topPos'    => 'color_bottom',
				'bottomCol' => 'color_top_position',
				'bottomPos' => 'color_bottom_position',
			),
		),
	),
) );
```

the `pattern_replace` argument as seen above is defined as an array.
The key in each "replacement" is a string that I used in the `value_pattern` argument, and the value is the name of the actual theme_mod I'm using.

Now let's see what our code would look like if we did the same for all 4 of our fields:

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

// Add Fields.
Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_top',
	'label'     => esc_attr__( 'Top Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#FFFFFF',
	'priority'  => 10,
	'output'    => array(
		array(
			'element'         => 'button',
			'property'        => 'background',
			'value_pattern'   => 'linear-gradient(to bottom, $ topPos%,bottomCol bottomPos%)',
			'pattern_replace' => array(
				'topPos'    => 'color_bottom',
				'bottomCol' => 'color_top_position',
				'bottomPos' => 'color_bottom_position',
			),
		),
	),
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'color',
	'settings'  => 'color_bottom',
	'label'     => esc_attr__( 'Bottom Color', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => '#F2F2F2',
	'priority'  => 11,
	'output'    => array(
		array(
			'element'         => 'button',
			'property'        => 'background',
			'value_pattern'   => 'linear-gradient(to bottom, topCol topPos%,$ bottomPos%)',
			'pattern_replace' => array(
				'topCol'    => 'color_top',
				'topPos'    => 'color_top_position',
				'bottomPos' => 'color_bottom_position',
			),
		),
	),
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'slider',
	'settings'  => 'color_top_position',
	'label'     => esc_attr__( 'Top Color Position', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => 0,
	'priority'  => 12,
	'choices'   => array(
		'min'  => 0,
		'max'  => 100,
		'step' => 1,
	),
    'output'    => array(
		array(
			'element'         => 'button',
			'property'        => 'background',
			'value_pattern'   => 'linear-gradient(to bottom, topCol $%,bottomCol bottomPos%)',
			'pattern_replace' => array(
				'topCol'    => 'color_top',
				'bottomCol' => 'color_bottom',
				'bottomPos' => 'color_bottom_position',
			),
		),
	),
) );

Kirki::add_field( 'mytheme', array(
	'type'      => 'slider',
	'settings'  => 'color_bottom_position',
	'label'     => esc_attr__( 'Bottom Color Position', 'mytheme' ),
	'section'   => 'mytheme_new_section_name',
	'default'   => 0,
	'priority'  => 13,
	'choices'   => array(
		'min'  => 0,
		'max'  => 100,
		'step' => 1,
	),
    'output'    => array(
		array(
			'element'         => 'button',
			'property'        => 'background',
			'value_pattern'   => 'linear-gradient(to bottom, topCol topPos%,bottomCol $%)',
			'pattern_replace' => array(
				'topCol'    => 'color_top',
				'topPos'    => 'color_top_position',
				'bottomCol' => 'color_bottom',
			),
		),
	),
) );
```

As you can see in the example above, the `output` argument for all the fields in similar.
The difference in each case is that `$` is used as a placeholder for the **current** setting, and the replacements are there for the remaining settings (all but the current).

You can see an example of the above code functioning in this short screencast:

<img src="/assets/article_images/2017-07-04-customizer-output-part-2/gradient-test.gif" style="width:100%;">

You'll also notice that we don't use `postMessage` but instead use `refresh` for this demo. This will change in a future version and support for `'transport' => 'auto'` will be added for pattern replacements as well.

<br><br>
### [Read Part 1: Automating CSS Output & postMessage scripts with Kirki](http://aristath.github.io/wordpress/customizer/2017/07/02/customizer-output-part-1.html)
