---
layout: post
title: "The Hidden Color Functions in WordPress"
permalink: "blog/the-hidden-color-functions-in-wp"
categories:
  - JavaScript
  - WordPress
comments_id: 9
---

Today I want to bring to your attention a hidden gen in WordPress. And I say "hidden" because in all my years in WordPress I've seen fery few developers use it - most likely because not everyone knows about it.

## Some background on _why_.

While working on an [implementation for accessible colors](https://github.com/WordPress/twentytwenty/pull/351) in the [twentytwenty](https://github.com/WordPress/twentytwenty) theme for WordPress 5.3 I needed a way to get various color properties like for example hue, saturation, lightness, relative luminance, contrast with other colors etc. All that so we could calculate colors that are readable and comply with WCAG rules for colors.  

Initially I started pulling functions from a [wcagColors.js](https://aristath.github.io/wcagColors.js/) library I wrote a while ago for the [Kirki WCAG Link Colorpicker](https://wplemon.com/downloads/kirki-wcag-link-colorpicker/) and [Kirki WCAG Text Colorpicker](https://wplemon.com/downloads/kirki-wcag-text-colorpicker/) controls. But these controls were a lot more complex than what we were trying to accomplish in the twentytwenty theme, so those were a bit of an overkill. A simpler solution was required. Enter WordPress's own `Color` functions.

## Where are they?

The reason you may have never heard of them is because they don't exist as a separate script. They should, but instead they're just [tucked away inside the iris colorpicker](https://github.com/Automattic/Iris/blob/858001593f00e6ad8028d4e1a7c48eccf55f75ac/dist/iris.js#L909-L1446). They are available as a separate script on [github.com/Automattic/Color.js](https://github.com/Automattic/Color.js) but it's been completely neglected, hasn't been updated since 2015.

## So how can you enqueue them?

Since they don't exist as a separate script, to use them you'll first have to make sure that the `wp-color-picker` script is enqueued. So you can simply add that as a dependency to your own scripts.

## Usage

Unfortunately the code is very poorly documented, so to get an idea of what you can do and how you can use it, you'd have to read the source. Or you can just keep reading. :)

### Getting properties of a color

First we need to instantiate the color:

```js
let myColor = Color( '#cd2653' );
```

Then we can get the properties we need:

```js
// Get red.
let red = myColor.r();
// Get green.
let green = myColor.g();
// Get blue.
let blue = myColor.b();
// Get alpha.
let alpha = myColor.a();
// Get hue.
let alpha = myColor.h();
// Get saturation.
let saturation = myColor.s();
// Get lightness.
let lightness = myColor.l();
```

The above are the basics, but there's more! You can also get the relative luminance of the color:

```js
let luminance = myColor.toLuminosity();
```

You can also get the [WCAG contrast](http://www.w3.org/TR/2008/REC-WCAG20-20081211/#contrast-ratiodef) between 2 colors:

```js
// Create background-color object.
let backgroundColor = Color( '#fff' );
// Create text-color object.
let textColor = Color( '#000' );

// Get the contrast between the 2 colors.
let contrast = textColor.getDistanceLuminosityFrom( backgroundColor );
```

If you want to get the color you should be using for your text depending on the background-color, there's a neat `getMaxContrastColor` function that will return a `Color` object of either white (`#fff`) or black (`#000`):

```js
// Get black/white Color object depending on background.
let textColor = Color( '#f2f2f2' ).getMaxContrastColor();
```

You can also automatically get a readable text-color:

```js
// Create background-color object.
let backgroundColor = Color( '#fff' );
// Create text-color object.
let myColor = Color( '#cd2653' );

// Get a readable color, with a minimum contrast of 7:1
let readable = myColor.getReadableContrastingColor( backgroundColor, 7 );
```
The above is not what was used in the twentytwenty theme because we needed something a bit more customized than that, but the above method also works perfectly fine.

### Manipulating colors.

Retrieving color values is not the only thing you can do, you can also use this library to manipulate your colors.

The following functions are available:
* `darken`
* `lighten`
* `saturate`
* `desaturate`
* `toGrayscale`
* `getComplement`
* `getSplitComplement`
* `getAnalog`
* `getTetrad`
* `getTriad`

You can also use the `r`, `g`, `b`, `a`, `h`, `s`, `l` methods I mentioned above in the example for retrieving a value simply by defining a value inside them:

```js
// Darken color by 30%:
let myColor = Color( '#cd2653' ).darken( 30 );

// Change the red value.
let myColor = Color( '#cd2653' ).r( 50 );
```

### Cloning an object

If you don't want your calculations to be applied in-place but instead want to apply them to a clone of the object so that you can still have access to the unmodified object, you can use the `clone` method:

```js
let background = Color( '#fff' );
let otherBackground = background.clone();
```

### Getting the CSS value from a color object

Easy, just use the `toCSS` method:

```js
let background = Color( '#fff' );
background.darken( 30 );
let newValue = background.toCSS();
```

### Chaining

You can chain multiple functions to get what you need faster. In the example below we're getting the complementary color or our original value, then darkening it by 30%, desaturating it by 10% and returning the CSS value:

```js
let newColor = Color( '#cd2653' )
    .getComplement()
    .darken( 30 )
    .desaturate( 10 )
    .toCSS();
```

## Read the source

The above are nothing but scratching the surface, there's a lot more you can do. I suggest you [read the source](https://github.com/Automattic/Color.js/blob/master/src/color.js) of this neat little gem. It may not be properly documented, but the functions and parameters have very descriptive names so it's easy to understand.

A big thank you to [Matt Wiebe](https://twitter.com/mattwiebe) for building this. It's been a long time since you did this, but it's still amazing.