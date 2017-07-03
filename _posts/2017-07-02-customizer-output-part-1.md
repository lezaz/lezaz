---
layout: post
title: "Automating CSS Output & postMessage scripts with Kirki (Part 1)"
categories:
  - WordPress
  - Customizer
image: /assets/article_images/chuttersnap-233105.jpg
---

This will be the first of a series of posts on how to use one of my favorite features of the [Kirki Plugin](https://wordpress.org/plugins/kirki): Its ability to automate the CSS output and the generation of `postMessage` scripts for the Wordpress Customizer.

Let's say for example that you want to do the following:
* Create a Section in the customizer
* Add a new color field to that section
* When the color in the control changes, change the background color of the `#content` element in your page
* The above change has to live-update the customizer preview pane without a refresh.

Using the Customizer API you would write something like this:

<script src="https://gist.github.com/aristath/7b0f744a45963536864697ba01eaa0b4.js"></script>

Then you'd write JS similar to this in your `/assets/js/theme-customizer.js` file inside your theme:

<script src="https://gist.github.com/aristath/b4b460cd47a1f54f4ff4e134b3d175a0.js"></script>

Using Kirki however, we can simplify all the above and write this in a PHP file:

<script src="https://gist.github.com/aristath/afb2fa3d8be080f308f9e19cb2c500f1.js"></script>

The above 2 methods have the exact same result, but the 2nd method is easier to understand and you don't need to write any custom sanitization functions (they are automatically applied), you don't have to write a custom function to output your CSS, and finally you don't have to write any custom JS either. The combination of the `output` arument with `'transport' => 'auto'` takes care of the JS generation for you. Isn't that cool? The `output` argument is defined as an array of arrays so if you want you can make the same color apply to multiple things. For example you can write something like this:

<script src="https://gist.github.com/aristath/14470e9b46fb76a2176319120af00577.js"></script>

The above will make the color you select be applied as a `background-color` to the `#content` element and also as text color in `#sidebar-1`.

Any changes you make to the `output` argument will automatically be applied both to the CSS that's generated for the frontend view of your site as well as the `postMessage` script.

> That's all nice, but I writing my own scripts allows be greater versatility.

True, but don't forget that the above is a pretty simple example and I didn't use any of the more advanced features/arguments from Kirki. We'll examine those in future posts in more detail.

Even though Kirki simplifies some things and makes life easier, it's very important for developers to start without Kirki. You will have to learn the Customizer API and the way things work if you want to build things properly.

I'll close this post with a work of caution:

**If you think of a theme you're building as a house, then WordPress core and its APIs are the materials you'll be using. Kirki is nothing more than a tool. Learn your materials before you pick up a tool, otherwise you'll end up breaking things.**