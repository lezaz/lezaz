---
layout: blog
title:  "Remotely installing plugins from an EDD site"
permalink: blog/edd-remote-installer
---

For a while now I've wanted to be able to remotely install WordPress plugins and themes from a site running[EDD](http://easydigitaldownloads.com). Sort of like the default WordPress plugin installer where you see a plugin, click on an "install" button and then the plugin is automatically downloaded and installed on your site.

There's a commercial plugin out there called [EDD Remote Install](https://mindsharelabs.com/downloads/edd-remote-install/) that does this, but for some reason it is no longer beeing developed.

I wanted an open-source alternative, one where the whole EDD Community could collaborate so that we may end up with something great.
So, the EDD-Remote-Install**er** was born.
Now you can see an alpha version of this on [https://github.com/aristath/edd-remote-installer](https://github.com/aristath/edd-remote-installer).
So far it seems to be working fine, with the exception of error and success messages. These need some work...

The repository consists of 2 parts: The client and the server plugin. The Server plugin goes on your EDD server and you can select there a category that contains your WordPress plugins, as well as a category that contains your WordPress themes.
This is a necessary step because it's not necessary that all your downloadable products are WordPress plugins and themes.
Once you select these categories, a json feed is available on your server that the client can access.

Our sample client plugin gets the json from your server (as a test site we're using [press.codes](http://press.codes)), parses that and creates an admin screen with all the plugins that can be installed.

So far only the plugins can be installed and we have not worked on the themes installer at all.

This is more like a proof of concept for now so there are some minor bugs, no caching etc, but we're optimistic that it will be ready for production sites soon enough.

In the meantime feel free to take it for a spin, and evern better help us improve it by submitting your pull requests. :)

Cheers!
Ari.
