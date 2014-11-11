---
layout: blog
title:  "Remotely installing plugins from an EDD site"
permalink: blog/edd-remote-installer
---

TLDR: <a class="btn btn-primary" href="https://github.com/aristath/edd-remote-installer">GET THE PLUGIN ON GUTHUB</a>


For a while now I've wanted to be able to remotely install WordPress plugins and themes from a site running[EDD](http://easydigitaldownloads.com). Sort of like the default WordPress plugin installer where you see a plugin, click on an "install" button and then the plugin is automatically downloaded and installed on your site.

There's a commercial plugin out there called [EDD Remote Install](https://mindsharelabs.com/downloads/edd-remote-install/) that does this and I actually purchased long ago, but for some reason it is no longer being developed and maintained. I reached out to the developers and urged them to open-source it and make it available to the public if they no longer want to maintain it as that would be beneficial both for them and for the whole community.  
The outcome of that was not positive so I had to write from the ground up something that would be open-source, something that  all WordPress theme & plugin shops can use and benefit from.

The result of my efforts is the EDD-Remote-Install**er** plugin.  
You can get current vrsion on its [Github Repository](https://github.com/aristath/edd-remote-installer).

The repository actually has 2 plugins in it:

1. A plugin that is installed server-side
2. A client plugin.

Once you install the plugin on your server, you will have access to a new page under "settings".
This is a simple page with 3 options:

* Enable JSON
* Select a category for Plugins
* Select a category for Themes.

Once you enable the json option, your products that are categorised as WordPress plugins and themes are exposed to the client plugin. This of-course is optional because in your case you may want to hard-code the plugins/themes array in your client plugin that's all up to you!

The sample client plugin gets the json from your server (as a test site we're using [press.codes](http://press.codes)), parses that and creates an admin screen with all the plugins that can be installed. In the future we'll hopefully also allow installation of themes, but for the time being we want to get the plugins right.

Now here's the interesting part...  
If the plugin on the server is free, then it is immediately downloaded and installed, no questions asked (just like it happens for [wordpress.org plugins](http://wordpress.org/plugins)).  
However, if the plugin is NOT free, then the plugin assumes that you have the [EDD-Software Licensing](https://easydigitaldownloads.com/extensions/software-licensing/) plugin installed on your server and asks for a license. The client enters their license, and if it's valid then installation proceeds.

This is more like a proof of concept for now so there are some minor bugs, no caching etc, but we're optimistic that it will be ready for production sites soon enough. It's still at an early stage in its development, primarily because I don't know much about jQuery and JS as a whole.

So far it seems to be working fine, with the exception of error and success messages. These need some work...

In the meantime feel free to take it for a spin, and even better help us improve it by submitting your pull requests. :)

I hope you enjoy this as much as we do, and see the potential it has to boost your sales!

Cheers,
Ari.
