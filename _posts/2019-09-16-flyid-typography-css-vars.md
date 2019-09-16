---
layout: post
title: "Fluid Typography with CSS Variables"
permalink: "blog/fluid-typography-css-vars"
categories:
  - CSS
comments_id: 7
---

By "Fluid Typography" in this article I'm referring to typography that scales depending on the screen size.  
Traditionally sites have a fixed font-size, like for example `14px`. The problem with that approach is that devices nowadays vary a lot. There are 4-inch smartphones, 40-inch Smart-TVs and everything in between.

Using CSS-Variables (aka _Custom Properties_) is not necessary, you can achieve the same result fine without them and I'll also be showing an example of that.

First of all, the code we'll be talking about:
```css
:root {
  --base-font-size: 12px;
  --fluid-typography-ratio: 0.9;

  --rem: calc(var(--base-font-size) + var(--fluid-typography-ratio) * 1vw);

  font-size: var(--rem);
}
```

The above is a simple technique that allows the font-size of your site to be readable no matter the device used. In fact that is the exact code used on this site.  
Now to explain what that code does:

First we're defining 2 values: `--base-font-size`, and `--fluid-typography-ratio`. Feel free to tweak these values to achieve the result you want, these 2 are responsible for the rest of the site and calculations.

Next we're defining the `--rem` variable.  
This is a value that gets calculated from the font-size and typography-ratio we defined.

If you're reading this post on a device that has a screen-width of `500px` then the font-size will be calculated like this:
```
12px + ( 500px / 100 ) * 0.9 = 17px
```
The `( 500px / 100 )` is because we used `1vw` which according to this [article on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/length) is defined as

> Equal to 1% of the width of the viewport's initial containing block.

So on a 500px-wide screen, 1vw is equal to `500px/100`.

If on the other hand you're reading this post on a 3000px-wide screen, then the font-size will be 
```
12px + ( 3000px / 100 ) * 0.9 = 42px
```

## Without CSS Variables

To do the same without CSS Variables you can replace the whole block of code from the beginning of this post with this:
```css
:root {
  font-size: calc(12px + 0.9vw);
}
```

Depending on your use-case that might be preferable since it's a lot shorter. In my case I'm using the base-font-size and typography-ratio variables for a bunch of other calculations too so it makes sense to use them.

## If you're building WordPress Themes

In case you're a WordPress Theme Developer the above method can be particularly useful and can be achieved with 2 simple controls in the WordPress Customizer:
* A slider control for the base font-size
* A slider control for the typography-ratio.

If users set the typography-ratio to `0` then their sites will use a static font-size, otherwise the values they define in these 2 controls will change the sizes of everything on their site. Take a lookat the [Gridd](https://wordpress.org/themes/gridd) for an example of how this can be done.

## Working with the above approach on a site

Though the above method is awesome and allows you to build more responsive layouts, you should be aware that you'll need to consistently use relative units on your site instead of absolute for most things. So for sizing elements that contain text you can use `em`, `rem`, `ch`, `vw`, `%` etc. **But not `px`**. The reason is simple: If you define your site-width as `960px` for example, then on a screen with a horizontal resolution of more than 3000px only a few characters will fit. If on the other hand you define your site-width as `65rem`, then the whole layout of the site resize to fit along with your text. And yes, you can use `rem` & `em` for your media-queries too.

This is one of the things I'd like to see more of on the web: Please move away from absolute values and start using more relative values. We no longer live in a world where all screens are between 15 and 19 inches, those days are long gone. Our designs should fit the medium they are consumed in.