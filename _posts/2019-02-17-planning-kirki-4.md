---
layout: post
title: "The future of Kirki - Planning v4.0"
permalink: "wordpress/customizer/2019/02/17/planning-kirki-4"
categories:
  - WordPress
  - Customizer
---

This year Kirki got **5 years old** and broke the **2 million downloads** barrier.
It was a big year with a lot of improvements in all areas of the plugin.
I wrote a lot of code, replaced a big part of our old codebase and there were major refactors across the board. As a developer I grow through Kirki, I discover new techniques and strive to improve it all the time.
However, while working on the Kirki plugin in 2018 one thing became evident. **Things need to change**. Kirki can't move forward in its current form. It is bloated with a lot of code that the majority of theme developers will never use, or code that they use and I wish they never did (like for example using the `customizer-styling` module).

I don't want to bore you with a lot of details, so these are the key areas I found problemmatic while working on Kirki this past year - and forgive me if some points are too blunt, I'm not exactly known as a diplomat.:

* Codebase is huge.
* Innefficient code.
* Hard to maintain.
* Not easy to contribute to.
* Doesn't use modern techniques.
* People want to contribute but I don't like the things they want (or need) to add to the plugin.
* Bad documentation

I mentioned in the intro that _"Things need to change"_. I know, nobody likes change and change usually means extra work. But in all honesty I don't think Kirki can survive without change.

After a lot of thought I finally reached the decission that change must be radical and ideally it would solve all of the above problems at once. So here's the plan:

## Drop PHP 5.2 support

Future versions of Kirki will only support PHP 5.6+.  
I'd love to do PHP7+ only, but there are a lot of themes out there that use Kirki and dropping anything below PHP7 would alienate a lot of theme developers. It would be a bad move, and since WordPress 5.2 (scheduled for April) will also drop support for PHP versions lower than 5.6, it seems like a reasonable compromise to do the same and not opt for PHP7+.

## Everything is a package

Now this is a fundamental change in the plugin's structure and concept. Until now, if a theme wanted to use Kirki they had to recommend users install it as a plugin - or bundle the plugin files in the theme.

In version 4.0 however, that will change. Each module will be a separate composer package, each control will also be a separate package, even the plugin core files will be a package. The only thing that will not be a package is the main plugin file which will be used to bootstrap everything else.

The benefits of the above implementation once complete are huge:  
If a theme just wants to use the repeater control, they will be able to to `composer require kirki-framework/control-repeater` in their theme. The control will be downloaded along with any dependencies it might have, and then developers will simply be able to use it. Don't need the automatic CSS output? No problem, don't install it!

Splitting the code to packages is already under way and I don't know if it will be complete before WordPress 5.2 is released, most probably it will be around the same time or about a month later. So expect this to happen by this spring.

This will solve a lot of problems:

* A lot easier to maintain
* Codebase will be significantly smaller
* Code will be rewritten for PHP 5.6+ where needed
* I won't necessarily be the sole maintainer for everything. Sure, I'm a bit of a control freak so I probably will, but at least there will be the possibility of finding a co-maintainer for specific packages if I manage to find someone who gains my trust and who has a good taste on code.
* It will be easier to contribute to. Contributions are nice, but on large projects sometimes contributions are large. And If you've ever had to review a large pull-request, you know that anything above a 500 to 700-line diff is practically unmanageable. Or if you think it's manageable it might be for you, but my brain just can't handle it. If packages are smaller, it will be easier for people to contribute to since they will only need to change a couple of files, and it will be easier for me to review and merge suggested changes. Everybody wins.
* Code-splitting will make it a lot easier to document things. Right now there's a big `docs` folder and everything's in there. If documentation is on a per-package basis and then somehow aggregated to the main site, then it will be easier to find areas that lack documentation and write it. When a package changes, the docs can change as well.
* More contributions. You don't need me to merge anything in the plugin, you just create your own controls as separate packages. Don't like my implementation for the CSS-output handlers? No problem, just fork that package, tweak it to your liking and use it in your project. If you think others will benefit from your tweaks create a pull-request and chances are it will be merged if it has quality code and improves things. Even the slightest improvement is welcomed!

A new organization was created for all the packages and you can track them on [github.com/kirki-framework](https://github.com/kirki-framework). There's also a separate [pull-request in the Kirki plugin](https://github.com/aristath/kirki/pull/2083). Keep in mind that this is still a work in progress so things are pretty fluid in there and they keep changing, but suggestions and discussion is more than welcomed in the PR.

## Backwards Compatibility

This has been the main reason that the above changes were not done sooner, i was looking for a way to make things work without breaking anything in the process. A solution was found and some of you will like it, some of you won't. But it's the safest solution I could think of.

Theme developers who only want to use parts of the Kirki plugin will be able to use the composer packages. But that doesn't mean that Kirki will stop being a plugin, on the contrary! The plugin will be a collection of all core modules and controls, BUT there will also be an additional package to ensure any syntax or API changes are backwards-compatible. This will act as a bridge for older implementations.

## Deprecations

While I do want to maintain backwards compatibility, there are parts of the code that simply have to be deleted. In 5 years of development whenever something changed I've always added workarounds and fallbacks for older implementations. In version 3.0.36 there are still many fallbacks for some obscure implementation that was added in version 0.3 back in 2014. Things like that only contribute to extra bloat and have outlived their usefulness.  
So yeah... If you built a theme back in 2016 and haven't updated it since then despite all the `_doing_it_wrong` notices I keep adding all these years, don't be surprised if something breaks.

## What happened to all the things you promised for v3.1?

4.0 is just a number. The 3.1 milestone will simply be renamed to 4.1 - though I expect most of the items to be implemented in v4.0. If something doesn't make it in 4.0, then at least this refactor will make all subsequent issues and features easier to resolve and implement.

## Monetizing

Kirki always has been and always will be free. That can't change, and even if it could I wouldn't want it to. I love open-souce, it's my way of giving back to the community and I sleep better at night. However there are some more advanced things that I sometimes want to do and they don't belong in a free plugin. 

Sometimes a control I build is too specific in scope and can only benefit a very small percentage of developers, ones that have the same passions as I (like for example accessibility, inclusive design, automating things to make life easier for users). When something is too specific, won't benefit the majority of users or is just too hard to accomplish on a free plugin (because that happens too), then it will be available as a premium addon. So far there are 4 such controls but in the future there will be more (and yes, a theme too). You can see all premium controls on [wplemon.com](https://wplemon.com/downloads/category/kirki-addons/).

------------------------------------------

This post turned out a bit bigger than I expected, if you got to the end thank you for reading all that stuff. Here's a lollipop. 

<img src="https://aristath.github.io/assets/article_images/lollipop.gif">