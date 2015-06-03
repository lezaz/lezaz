---
layout: post
title:  "Maera 1.2 released"
permalink: blog/maera-1.2-released
categories: WordPress
---

I'm happy to announce that Maera version 1.2 has just been released.

The changelog is big, but I'll cover some of the most important parts here.

## Don't know what Maera is?

In short, Maera is a theme framework allowing you to use [twig](http://twig.sensiolabs.org/) for your template files, and allows for a more modern and different approach to WordPress themes using shells: An easy way to completely customize the theme with your own markup, css, scripts and functionality.

## Plugins are now embedded

I know this is going to be a controversial decission, but we ran into some issues that made it really important for us to include required plugins in the theme instead of requiring our users to install them separately.  
These plugins are:
* Timber
* Kirki
* Jetpack

Of course we do the following check:  
If the plugin is already installed, then use the plugin version. If no plugin is found, then and only then we load the bundled version.

Timber had a few issues in its latest release and as a result a lot of our users had menus disappearing and the blog pages of their sites mis-behaving. We had to bundle a previous version of the plugin in the theme.
Kirki is a dependency for our customizer, and from Jetpack we were only using 2 classes on the core shell (the `Tonesque` and `Jetpack_Color` classes) so if Jetpack is not already installed on your site then these 2 classes will be included from the theme.

As a result of the above change we can now include a stable version of the plugins and one that we know works best with the theme and you won't have to worry about incompatibilities etc.

## Easy-Digital-Downloads integration

We used to have a separate, premium plugin for Easy Digital Downloads integration. This extra plugin has now been merged in the theme core and is available free.

## WooCommerce integration

Just like with Easy Digital Downloads, we retired the premium maera-woocommerce plugin that we had and this is now available in-core.

## Removed the admin pages

We had some options in an admin page. This has now been deprecated and moved to the customizer. What that means is that now you can change shells from inside the customizer. The only downside to this is that you will have to refresh your browser after you choose a different shell and save your selection because the implementation of our shells does not currently support auto-refreshing the customizer screen.

## Use dashicons instead of elusive icons

Since Dashicons are included with WordPress there really was no real poin in using something other than that, so we deprecated the use of elusive-icons and switched to using dashicons.

## Other tweaks and improvements

Of course these are not the only things that changed... We also made a lot of other minor improvements, tweaks and fixed some minor bugs. You can find the complete list of changes if you want in the [commits list](https://github.com/presscodes/maera/commits/master) in our github repository for the theme.
