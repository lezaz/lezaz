---
layout: post
title:  "I like Jetpack - You should too."
permalink: blog/why-i-like-jetpack
categories: WordPress
author_name : Ari Stathopoulos
author_avatar: aristath
show_avatar : false
feature_image: reunion-wallpaper-2560x1600.jpg
show_related_posts: false
---

Jetpack is one of the most controversial WordPress plugins. It's one of the most well-known plugins, used by millions. However, there are those who just don't like it. We've all heard people say they don't use it 'cause it's bloated, it's full of things we don't really need and so on.

Well, today I'm going to share with you why I **do** like it and how we use it for the sites we build.

Jetpack is indeed a big plugin. it's also one of the most well-maintained plugins available today and is always up to date. You could describe Jetpack as the sum of smaller plugins:

* Beautiful Math
* Carousel
* Contact Form
* Custom CSS
* Custom Content Types
* Enhanced Distribution
* Extra Sidebar Widgets
* Gravatar Hovercards
* Infinite Scroll
* JSON API
* Jetpack Comments
* Jetpack Single Sign On
* Likes
* Manage
* Markdown
* Mobile Theme
* Monitor
* Notifications
* Omnisearch
* Photon
* Post by Email
* Protect
* Publicize
* Related Posts
* Sharing
* Shortcode Embeds
* Site Icon
* Site Verification
* Spelling and Grammar
* Subscriptions
* Tiled Galleries
* VideoPress
* WP.me Shortlinks
* Widget Visibility
* WordPress.com Stats
* VaultPress

It does look a bit bloated, right?

Well, yes and no. See Jetpack is modular. You can enable or disable any of these modules and use them at will. For example I've never used beautiful math 'cause I never needed to. These are the things I do use:

### Contact Form

Most sites need a contact form... and most people I've seen resort to using plugins like gravity forms, contact forms 7 etc, when all they actually do is have a contact form on their site. It doesn't make a lot of sense... it's simpler to just use Jetpack's contact forms instead. They are simple, secure, they work in combination with akismet and they can do pretty much anything you'd want a contact form to do.

### Custom CSS

When working on a client site, there are instances where you need to add some simple CSS without however editing the theme files. In these cases, the Custom CSS module is a life-saver.

### Enhanced Distribution

It's great for SEO, so why not!

### Infinite Scroll

Depending on your theme's implementation, it may support infinite scroll. If it does then Jetpack does all the heavy-lifting for you and makes everyone's life easier.

### Jetpack Single Sign On

I like allowing people to login to my site using their wordpress.com account... Most people prefer using social logins like twitter, facebook etc, but if you're running a WordPress-related business like I do, then allowing people to login using their wordpress.com account makes a lot more sense.

### Likes

Allows readers on our company blog to basically bookmark posts on their wordpress.com account.

### Manage

Though not quite like manageWP, it's pretty similar in philosophy and I'm sure some day it will be a lot better too. It allows us to manage a lot of sites from one single point so it's pretty useful if you run multiple sites.

### Markdown

If you're a developer and you use github a lot, then you're probably used to writing in Markdown.
This blog for example is built on Jekyll and hosted on github pages. I write natively using Markdown so I'm pretty much used to it. Using the Markdown module I'm able to write the same way in my WordPress sites.

### Monitor

I used to have a premium plan on pingdom.com.
The Monitor module however does pretty much the same thing: It sends me a notification when one of my sites is down. Sure, pingdom has cooler stats etc, but the Monitor plugin does just what I need: It monitors my sites for any downtime

### Omnisearch

I like being able to search for anything from my dashboard, so this is a great addition

### Photon

Instant CDN for my site's images. What more can you ask for!

### Protect

Protects from brute-force attacks.

### Publicize

Allows me to automatically post new posts to social networks

### Sharing

Adds social share buttons to my posts. It's lightweight and pretty nice!

### Widget visibility

I usually don't want all my widgets to show up everywhere... This module allows me to select where I want each widget to show up. Pretty neat!

### WordPress.com Stats

Think of it like light-weight google analytics. I rarely look at my analytics account anymore... I get an overview of what goes on in my dashboard and if I see something out of the ordinary, then I'll go to my analytics account and dig deeper.

---------------------------------------

The above are the modules that I use 90% of the time. If it were not for Jetpack, I'd need a security plugin, a CDN plugin, a dashboard analytics plugin, a social sharing plugin, an SEO plugin, a pingdom account, a ManageWP account, I wouldn't be able to write using Markdown, I'd have to write my own implementations for infinite scroll, I'd use a separate plugin for custom CSS, and a pretty heavier plugin for my contact forms.

The people that criticize Jetpack usually have all of the above in one form or the other... the only difference is that they use different plugins by different developers, not as frequently updated, not as well tested, and as a result not as well thought-of.

But if you think that's all there is to Jetpack you're wrong!!

If you want to dig a bit deeper, there's even more things you can use!

* The Tonesque library allows you to detect the dominant color of an image and use it in your theme
* The `Jetpack_Color` class allows you to manipulate colors in your theme
* It has a php-less compiler
* It has a php-sass compiler
* Lots of other small things that you will have to discover by simply experimenting with it and looking at its brilliantly-written code

At [PressCodes](https://press.codes) we recently released the [Maera](maera.io) theme framework. One of the eccentricities of this framework is that it **requires** you to install Jetpack. Why you ask?

Try allo of the above!! We needed a way to manipulate the color of a page depending on the featured image of a post, we needed to auto-calculate some color values depending on the user's selections in the customizer, we needed a compiler, and we did not want to re-invent the wheel. We needed something that we knew would be perfect and so we decided to go with Jetpack.

If you're a theme developer then you really should take a look at the way Jetpack does some things...

Scratch that. If you're a WordPress developer in general, then you **need to** examine Jetpack's code in detail. It's one of the most well-written plugins available today and you should not turn your back to it. I know 'cause I did that in the past. I listened to some people saying how bloated it is and it did make sense. They had a point. But in order to achieve the same things that Jetpack could do for them, their sites were a lot more bloated and heavier that what they would be if they simply used Jetpack.

Don't do the same mistake.
