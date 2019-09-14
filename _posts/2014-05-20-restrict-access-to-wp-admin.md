---
layout: post
title:  "How to restrict access to the WordPress Admin"
permalink: blog/restrict-access-wordpress-dashboard
categories:
    - Administration
    - WordPress
---

Sometimes when you build a WordPress multisite installation that will be accessible by many people, you want to restrict access to certain areas of your site.
The reasons behind such a decision may vary: Some want to simply make it easier for their clients to navigate the dashboard by removing all the clutter and others want to protect sensitive areas of their sites.

Sure there are some excellent plugins out there that you can use in order to achieve this, but most of the times you can achieve it with some simple custom code so there's no need to add a new plugin on your site.

This process can be usually divided to 2 steps:

* Remove unnecessary admin menus
* Restrict access to some pages.

I'm going to simply copy-paste some example code below that you can use in order to achieve both of these goals and you can get them and tweak them on your own. :)

#### Step 1: Remove unnecessary pages.

The below function actually contains 2 completely different methods, you can use whichever you want. SOmetimes some menus are not removed using the first method and the 2nd one has to be used instead. Just try it on your own.

```php
<?php

/*
 * Hide admin menus for non Network Admins
 */
function custom_remove_admin_theme_remove_menus () {
	if( !current_user_can( 'manage_network' ) ) {
		global $menu;
		$restricted = array(_
			__( 'Posts' ),
			__( 'Media' ),
			__( 'Links' ),
			__( 'Pages' ),
			__( 'Tools' ),
			__( 'Users' ),
			__( 'Settings' ),
			__( 'Comments' ),
			__( 'Plugins' ),
			__( 'QRcode'),
		);
		end ( $menu );

		while ( prev( $menu ) ) {
			$value = explode( ' ',$menu[key( $menu )][0] );
			if ( in_array( $value[0] != NULL ? $value[0]: '', $restricted ) ) {
				unset( $menu[key( $menu )] );
			}
		}

		remove_menu_page( 'edit-comments.php' );
		remove_menu_page( 'themes.php' );
		remove_menu_page( 'plugins.php' );
		remove_menu_page( 'admin.php?page=mp_st' );
		remove_menu_page( 'admin.php?page=cp_main' );
		remove_submenu_page( 'edit.php?post_type=product', 'edit-tags.php?taxonomy=product_category&amp;post_type=product' );
		remove_submenu_page( 'edit.php?post_type=product', 'edit-tags.php?taxonomy=brand&amp;post_type=product' );
		remove_submenu_page( 'edit.php?post_type=product', 'edit-tags.php?taxonomy=model&amp;post_type=product' );
		remove_submenu_page( 'edit.php?post_type=product', 'edit-tags.php?taxonomy=product_tag&amp;post_type=product' );
	}
}
add_action('admin_menu', 'custom_remove_admin_theme_remove_menus', 10);

?>
```

#### Step 2: Disallow access to certain pages

This is quite simple...
First we build an array of URLs we don't want our users to be able to access.
Once we do that, then we simply build our function that will redirect them to their dashboard page instead:

```php
<?php

/*
 * If user is not a SuperAdmin, when they try to access the below URLs they are redirected back to the dashboard.
 */
function restrict_admin_with_redirect() {

	$restrictions = array(
		'/wp-admin/widgets.php'
		'/wp-admin/widgets.php'
		'/wp-admin/user-new.php'
		'/wp-admin/upgrade-functions.php'
		'/wp-admin/upgrade.php'
		'/wp-admin/themes.php'
		'/wp-admin/theme-install.php'
		'/wp-admin/theme-editor.php'
		'/wp-admin/setup-config.php'
		'/wp-admin/plugins.php'
		'/wp-admin/plugin-install.php'
		'/wp-admin/options-writing.php'
		'/wp-admin/options-reading.php'
		'/wp-admin/options-privacy.php'
		'/wp-admin/options-permalink.php'
		'/wp-admin/options-media.php'
		'/wp-admin/options-head.php'
		'/wp-admin/options-general.php.php'
		'/wp-admin/options-discussion.php'
		'/wp-admin/options.php'
		'/wp-admin/network.php'
		'/wp-admin/ms-users.php'
		'/wp-admin/ms-upgrade-network.php'
		'/wp-admin/ms-themes.php'
		'/wp-admin/ms-sites.php'
		'/wp-admin/ms-options.php'
		'/wp-admin/ms-edit.php'
		'/wp-admin/ms-delete-site.php'
		'/wp-admin/ms-admin.php'
		'/wp-admin/moderation.php'
		'/wp-admin/menu-header.php'
		'/wp-admin/menu.php'
		'/wp-admin/edit-tags.php'
		'/wp-admin/edit-tag-form.php'
		'/wp-admin/edit-link-form.php'
		'/wp-admin/edit-comments.php'
		'/wp-admin/credits.php'
		'/wp-admin/about.php'
	);

	foreach ( $restrictions as $restriction ) {

		if ( ! current_user_can( 'manage_network' ) && $_SERVER['PHP_SELF'] == $restriction ) {
			wp_redirect( admin_url() );
			exit;
		}

	}

}
add_action( 'admin_init', 'restrict_admin_with_redirect' );

?>
```

In some cases, depending on your site's use case, you might want to only allow dashboard access to an administrator for example.
In that case, you can use the following:

```php
<?php

/*
 * If user is not an admin, do not allow access to the dashboard AT ALL.
 */
function custom_remove_no_admin_access(){
	if ( ! defined( 'DOING_AJAX' ) && ! current_user_can( 'manage_options' ) ) {
		wp_redirect( home_url() );
		die();
	}
}
add_action( 'admin_init', 'custom_remove_no_admin_access', 1 );

?>
```
