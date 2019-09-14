---
layout: post
title:  "How to change the width of the customizer"
permalink: blog/howto-change-customizer-width
skip_from_archives: true
categories:
  - WordPress
  - Customizer
---

Depending on your use-case, you may want to change the width of your customizer, thus having more real-estate to handle your custom controls and content. You can do that by adding some custom CSS.

### Using the WordPress API:

First, add a new CSS file in your plugin/theme with the following in it:

```css
.wp-full-overlay-sidebar {
    width: 400px;
}

.wp-full-overlay.expanded {
    margin-left: 400px;
}
```

If this is a theme we're talking about, then I'll assume this file is located in `{THEME}/assets/css/customizer.css`.

Then, add the following to your `functions.php` file:

```php
<?php

function add_my_customizer_styles() {
    wp_enqueue_style( 'my-customizer-css', trailingslashit( get_template_directory_uri() ).'assets/css/customizer.css', null );
}
add_action( 'customize_controls_print_styles', 'add_my_customizer_styles', 99 );

?>
```

### Using Kirki

Alternatively, if you're using the [Kirki Toolkit](http://kirki.org) plugin you can do it by using the `width` argument in your configuration. For more info please check out the plugin's [Wiki page](https://github.com/reduxframework/kirki/wiki/Styling-the-Customizer).
