---
layout: post
title:  "How to use the customizer to customize your theme template files"
permalink: blog/customize-templates-using-kirki
categories:
  - WordPress
  - Customizer
---
A while back I was experimenting with a new concept for a theme where users would be able to customize the way their templates are structured. They would be able to select what elements they wanted active and in what order these would be.  
This was just an experiment but it was actually quite successful and you can see a demo of it here (skip to 1:28)

<iframe width="420" height="315" src="https://www.youtube.com/embed/-H6cLI2xabE" frameborder="0" allowfullscreen></iframe>

A few hours ago I got an email that said the following:

> I would like to give you a compliment about the Kirki plugin, it’s in one word AMAZING! Thanks therefor. I’ve got a little question since I’ve got no idea how to use the Sortable Control. Is there a tutorial who to achieve the same results like you’ve shared on Youtube in this movie: [https://www.youtube.com/watch?v=-H6cLI2xabE&feature=youtu.be&t=1m28s](https://www.youtube.com/watch?v=-H6cLI2xabE&feature=youtu.be&t=1m28s) with the Sortable Control?

So, I decided to write a new post explaining how to do it.

### Install Kirki

The steps below can be done without the Kirki plugin, but in case you don't want to use it you'll have to get the `sortable` control and custom-code it in your theme, including its JS & CSS files. If you can already do that then obviously this tutorial is not for you. :)

For simplicity's sake I'm going to use the [Kirki](https://plugins/kirki) plugin.

### Add the customizer field

The code below will add a new field to the customizer using the `sortable` control (see the [field docs](https://github.com/aristath/kirki/wiki/sortable) for more info). I'm going to assume that the field will be added to a section called `single_post` - You can change that to a section you already have in your theme or see the docs on [creating sections](https://github.com/aristath/kirki/wiki/Sections).

```php
<?php

Kirki::add_field( '', array(
    'type'        => 'sortable',
    'settings'    => 'single_post_template_parts',
    'label'       => __( 'Template parts for single posts', 'kirki' ),
    'description' => __( 'You can enable/disable the template parts that interest you below and reorder them to your liking.', 'kirki' ),
    'section'     => 'single_post',
    'default'     => array(
        'title',
        'meta',
        'content'
    ),
    'choices'     => array(
        'title'          => __( 'Title', 'kirki' ),
        'meta'           => __( 'Meta', 'kirki' ),
        'featured-image' => __( 'Featured Image', 'kirki' ),
        'content'        => __( 'Content', 'kirki' ),
        'author'         => __( 'Author', 'kirki' ),
        'comments'       => __( 'Comments', 'kirki' ),
    ),
    'priority'    => 10,
) );

?>
```

### Edit the template file

Now that we have the customizer field we have to edit our template file and modify it so that the user's selections are actually applied to the theme.
Since the above control was for single posts. we'll edit the `single.php` file.

This is an over-simplified template file but it should demonstrate what needs to be done:

```php
<?php

/**
 * Get the header
 */
get_header();

/**
 * Get the 'single_post_template_parts' theme_mod
 * It's defined as an array of elements so default is an array
 */
$template_parts = get_theme_mod( 'single_post_template_parts', array( 'title', 'meta', 'content' ) );

/**
 * If $template_parts is not empty and is an array
 * then we'll call a template part for each of the items in the array.
 */
if ( ! empty( $template_parts ) && is_array( $template_parts ) ) {
    foreach ( $template_parts as $part ) {
        get_template_part( 'template-parts/single-' . $part );
    }
}

/**
 * Get the footer();
 */
get_footer();

?>
```

That's it... pretty simple, right?

Now all we have to do is create our template parts in the theme's 'template-parts' folder and use filenames of the form `single-{part}.php`, replacing `{part}` with our options (example: `single-title.php`, `single-meta.php`, `single-content.php`, `single-featured-image.php` and so on).

I think this is one of the most interesting uses of the customizer... and I can't wait to see what you guys come up with and how you use it.

## UPDATE

Further discussion on that same email revealed that [@nickheurter](https://twitter.com/nickheurter) had something different in mind! So here's another use-case:

Nick has text fields for social network links. I'm goig to assume for this example's sake that the text fields IDs are `{social_network_name}_link`.

Then there is a sortable control that allows users to select which networks they want to activate and their order.
For the sake of this example, I'll assume that the ID for this field is `social_networks_order`.

To understand what we mean with above, just take a look at these 2 screenshots:

<img src="/images/social-links1.png"><img src="/images/social-links2.png">

I'm going to show 2 ways to do this in the template file:

Assuming that Nick uses font-awesome in his theme (or something similar like [socicon](http://www.socicon.com/)) and the array of networks he has is defined using sane array keys (facebook, twitter etc instead of custom names) then this could be as simple as this:

```php
<?php
/**
 * Get the social_networks_order theme_mod
 * then loop through its active items and generate the links/icons
 */
?>
<?php $active_networks = get_theme_mod( 'social_networks_order', array() ); ?>
<?php if ( ! empty( $active_networks ) && is_array( $active_networks ) ) : ?>
    <?php foreach ( $active_networks as $social_network_id => $social_network_label ) : ?>
        <a href="<?php echo esc_attr( get_theme_mod( $social_network_id . '_link', '' ) ); ?>">
            <i class="fa-<?php echo esc_attr( get_theme_mod( $social_network_id . '_link', '' ) ); ?>"
            <?php echo esc_textarea( $social_network_label ); ?>
        </a>
    <?php endforeach; ?>
<?php endif; ?>
```

In case however the implementation is a bit more custom - or we want to do something a bit more advanced/different on a per-network basis then we'd do something like this:

```php
<?php
/**
 * Get the social_networks_order theme_mod
 * then loop through its active items and generate the links/icons
 */
?>
<?php $active_networks = get_theme_mod( 'social_networks_order', array() ); ?>
<?php if ( ! empty( $active_networks ) && is_array( $active_networks ) ) : ?>
    <?php foreach ( $active_networks as $social_network_id => $social_network_label ) : ?>
        <?php if ( 'facebook' == $social_network_id ) : ?>
            DO SOMETHING FOR FACEBOOK
        <?php elseif ( 'twitter' == $social_network_id ) : ?>
            DO SOMETHING FOR TWITTER
        <?php else : ?>
            YOU GET THE POINT... :)
        <?php endif; ?>
    <?php endforeach; ?>
<?php endif; ?>
```
I hope that helps!

Cheers,
Ari.
