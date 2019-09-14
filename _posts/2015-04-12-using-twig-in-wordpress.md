---
layout: post
title:  "Why you should use twig for WordPress"
permalink: blog/using-twig-in-wordpress
categories: WordPress
---

WordPress is a great platform and it's as versatile as a platform can be. Proof of that is that it currently powers more than 23% of the internet as we know it. Historically though that has not always been the case and there's one thing that made WordPress stand out: **Themes**.

The WordPress theming industry (by the way, is it theming or themeing? Is that even a word?) is pretty large and everyday we see new businesses pop-up, offering their own themes. The choices are practically limitless and you can find whatever you need.

One of WordPress's strong points is also -in my opinion- its weakest point. **WordPress is built on PHP**. Don't get me wrong, I love PHP! It's easy to understand, easy to write and you can do anything you want with it.
However,
> PHP was never designed to be a templating language.

Some CMSs in order to address that problem have decided to make things a bit better for their developers and use a _real_ templating language for their templates. [Drupal](https://www.drupal.org/) and [Laravel](http://laravel.com/) both work with [Symphony](http://symfony.com/), which in turn utilizes the [twig templating engine](http://twig.sensiolabs.org/).

## What is [twig](http://twig.sensiolabs.org/)?

> Twig is a modern template engine for PHP

Simply put, twig allows you to write more efficient templates using a syntax that pretty close to plain HTML and is easy to understand and digest. It simplifies a lot of things and looks a lot like [mustache](http://mustache.github.io/) if you're familiar with it.

It helps separate logic from templates and allows to to write better, more efficient and easier to maintain code.

## How can you use it in WordPress?

There are a couple implementations out there, but the most mature I've found and the one I use is [Timber](http://jarednova.github.io/timber/).

Timber is a WordPress plugin you can install on your site and allows you to immediately start using twig in your templates.

Here's what a normal PHP Loop would look in a WordPress theme:

<script src="https://gist.github.com/aristath/778ff9f96e04b8e2fc5b.js"></script>

and here's the same thing written with twig:

<script src="https://gist.github.com/aristath/a05e1f27329c19329381.js"></script>

As you can see, the example using timber is a lot simpler to write and a lot easier to understand! When you look at you know **exactly** what goes on and there's no logic involved in it. It's a pure template. Using twig, someone that sees your code for the first time will be able to understand it and tweak it, write their own templates and extend it. As for the logic behind the template, this does not belong in the template _view_ so it's included in a PHP file where you can write all your logic, separating logic from templates.

**How can you actually do that?** Thankfully Timber has an excellent documentation site explaining how you can use it: [github.com/jarednova/timber/wiki](https://github.com/jarednova/timber/wiki)

The docs are well-written and cover almost everything from simple topics to advanced techniques and extending twig.

If you want to take a look at a WordPress theme that's actually using timber, you can take a look at [Maera](https://github.com/presscodes/maera) and its [documentation site](http://maera.io/).


If you're a WordPress theme developer I hope you'll take a look at twig and start using it on your own themes. It's a really great step to the right direction! :)

Cheers!
