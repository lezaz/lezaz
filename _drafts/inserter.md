---
layout: post
title: "Using the inserter plugin"
categories:
  - WordPress
  - REST API
image: /assets/article_images/chuttersnap-233105.jpg
---

Last week I released a new plugin on wordpress.org called [Inserter](https://wordpress.org/plugins/inserter). The name seemed appropriate, and you'll realize why as I explain below what it does.

Short explanation: Inserter allows you to write custom HTML & JS in your dashboard, and then replace the contents of any element in your screen (or append your content to it in v1.1).

The technical explanation: Inserter has a custom post-type where you can write a custom template-part using underscore.js. If you've worked with the customizer and creating custom controls for it, you're probably already familiar with the underscore.js syntax. :)
You can feed the global `$post` to your custom template, use a custom JSON array of data, or even use the REST API from WordPress and template is then rendered on your site.

Now, it may sound pretty simple, but you can actually do lots of cool things using it!

One of the cool things I like is skeleton templates. 
