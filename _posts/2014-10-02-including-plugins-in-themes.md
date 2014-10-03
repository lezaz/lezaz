---
layout: blog
title:  "Should we include plugins in the Maera theme?"
permalink: blog/including-plugins-in-themes-dilemma
---

This blog post is going to be alittle different than my previous ones...  
This one is more of a question.

While developing the [Maera](https://github.com/wpmu/maera) theme, we needed to include libraries and scripts that are already present in other plugins. These include:

* [Kirki](http://kirki.org)
* [Timber Library](https://wordpress.org/plugins/timber-library/)
* [Less PHP Compiler](https://wordpress.org/plugins/lessphp/)
* A couple of classes from [Jetpack](https://wordpress.org/plugins/jetpack/)
* Some more classes that though they are not already plugins, we could easily convert them to plugins.

There are a few options to implement these, each one of them has its pros and cons:

### 1. Embed the plugins in the theme

Pros:

* Easier for users. They are not required to install any 3rd party plugins to make the theme work as it was intended to work. They can simply activate it and use it immediately.
* We're always sure that the theme will not break by a major plugin update before we had the chance to review it and implement any modifications needed. Users will always get the latest tested and compatible version, with patches applied if these are needed.
* If the plugins are installed, they are loaded instead of the embedded versions of the theme.

Cons:

* If the user has not installed the plugins themselves, it's not always certain they'll get the latest version. We will have to manually update all of these in the theme itself and release updates.
* The theme does not pass wordpress.org tests because the compilers have some functions that require writing to the filesystem. It's secure and it's actually pretty standard for a lot of plugins, but the theme fails the automatic WordPress theme tests and therefore can't be submitted to the w.org repository.

### 2. Require users to manually install the plugins.

Pros: 

* Pretty easy to implement since we can use the [TGM Plugin Activation Class](http://tgmpluginactivation.com/) to require plugins and prevent the theme from being activated if these are not present.
* Users will always get automatic updates from wordpress.org for these 3rd party libraries.
* The theme will be able to pass the WordPress tests ad possibly be included in the wordpress.org themes repository, so more exposure.

Cons:

* Not as straightforward as embedding the plugins. Users will have to follow a process in order to start using the theme. This may be confusing to some and put them off.
* A major plugin update to one of these plugins may break the theme functionality if that update is not done in a backwards-compatible manner.


Best practices dictate that we follow the separate plugins route. However as you can see above it's a bit more complicated that that... We could of course follow a path that's somewhere in the middle:  
We could embed the plugins, AND the [TGM Plugin Activation Class](http://tgmpluginactivation.com/), advising users to instal the standalone plugins. If they install them then these will be used. If they don't install them then the embedded versions will be used instead.

What do you think?
What would you, as users and developers want?