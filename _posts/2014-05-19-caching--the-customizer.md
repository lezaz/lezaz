---
layout: default
title:  "Caching the WordPress Customizer"
permalink: blog/caching-customizer-css
---

A lot of themes use the WordPress customizer to generate additional CSS.
That CSS is then usually simply echoed to the `<head>` of the page.

But what happens when you have complicated calculations and/or a lot of styles you want to add? Usually that means that your webserver has to re-calculate everything on each page load.

That might be fine for some themes where for example all you have is the background color, but if you're like me and you're building something like the [Shoestrap](https://github.com/wpmu/shoestrap) theme, then you need something a little more powerful than that.

### Transients API to the resque.

Thanfully, WordPress has a lot of caching mechanisms.
One of them is the [Transients API](http://codex.wordpress.org/Transients_API) and it's what we'll be using here.

Usually in order to get a setting and echo it back to your `<head>` you'd do something like this:

<script src="https://gist.github.com/aristath/c3c89141164300c0cc98.js"></script>

What we'll do here, is introduce a proxy function.
The purpose of this will be to cache the resulting CSS in a transient in our database so that we don't have to re-calculate it each and every time:
<script src="https://gist.github.com/aristath/ff019d9ec231c75dc0f7.js"></script>

The above function will get the styles using a custom filter `my_styles_filter` and then saves it in the `my_customizer_styles` transient for 24 hours.
We've also added a check so that when you're in the customizer, you will not get the cached CSS but the live CSS instead.

The only problem with that implementation is that when you save your changes to the customizer, you will have to wait until the transient expires before you see the CSS changes go live on your site.

In order to correct this we have to write another function. One that will simply delete the existing transient when you hit the *save* button on your customizer:
<script src="https://gist.github.com/aristath/1e1c3574317a0db056e8.js"></script>

Now let's put all of the above together and we're done!
<script src="https://gist.github.com/aristath/554a89658cbb4cc7ce35.js"></script>

This is how we're implementing the customizer caching on the upcoming version of the Shoestrap theme, so if you want to take a look at a somewhat more complex implementation, feel free to [fork the theme on github](https://github.com/wpmu/shoestrap)!