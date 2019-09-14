---
layout: post
title: "Making Links Accessible Using Contrast"
categories:
  - Accessibility
permalink: accessibility/make-links-accessible
---

Accessibility is one of the hardest things to accomplish on a site. One of the confusing things when trying to make a website accessible, is choosing the right color for your links.

More specifically what we need for AA conformance is:

* A 4.5:1 contrast between the non-link text color and the background.
* A 4.5:1 contrast between the link text color and the background.
* A 3:1 contrast between the link text color and the surrounding non-link text color.

However, if you want to achieve AAA conformance, the requirements will change to this:

* A 7:1 contrast between the non-link text color and the background.
* A 7:1 contrast between the link text color and the background.
* A 3:1 contrast between the link text color and the surrounding non-link text color.

After a little experimentation I found that there's very little (if any) wiggle room in there. If your background color is not absolute white (`#FFF`) and the text color is not absolute black (`#000`), then there's absolutely no colors you can use for your links if you want to achieve AAA conformance.

While building an accessibility-ready theme for WordPress, I needed to auto-calculate the links colors based on a user-selectable color. So here's where the hard part of this journey began. How does one auto-calculate accessibility-ready colors that have a 7:1 contrast with white and 3:1 contrast with black?

The answer is simple (but painful)... you don't autocalculate them. The calculations in JS or PHP would be an unnecessary complexity. And since the wiggle room for these colors is basically non-existent, I chose to hard-code an array.

I chose to use `HSL` colors instead of `HEX` or `RGB` because they are simpler to work with. The colorpicker I'm implementing is a hue-only colorpicker (though you can use any colorpicker and then just extrapolate the hue value from hex/rgb), so that gives us the hue as an integer.

The end result for the link colors will be formatted like this:

```
hsl($hue, 50%, $lightness)
```

I chose to use a hard-coded value of `50%` for the saturation because any higher or lower and some colors just don't have a match for the 7:1 & 3:1 contrast ratios we're after.

After many hours of testing each and every hue value from 0 to 359 and checking which lightness is appropriate for each one to have the required contrast ratios I ended up with this array for the lightness:

```
[ 41, 41, 40.8, 40.4, 40, 39.7, 39.5, 39.1, 38.7, 38.4, 38.1, 37.5, 37, 36.7, 36.5, 36, 35.8, 35.5, 35, 34.8, 34.5, 34.2, 34, 34, 33, 33, 33, 32.5, 32, 32, 31.5, 31, 31, 30.5, 30.2, 30, 29.5, 29.3, 29, 29, 28.5, 28.2, 28, 28, 27.5, 27.3, 27, 27, 26.5, 26.3, 26.2, 26, 26, 25.3, 25.2, 25, 24.7, 24.6, 24.4, 24, 24, 24, 24, 24, 24, 24, 24.3, 24.4, 24.5, 24.5, 24.5, 24.6, 24.6, 24.7, 24.7, 24.7, 25, 25, 25, 25, 25.1, 25.1, 25.1, 25.2, 25.2, 25.3, 25.3, 25.4, 25.4, 25.5, 25.5, 25.6, 25.6, 25.7, 25.7, 26, 26, 26, 26, 26, 26, 26, 26, 26, 26.1, 26.1, 26.1, 26.1, 26.2, 26.2, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.8, 26.8, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.5, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26.1, 26, 26, 26, 26, 26, 26, 26, 26, 26, 26, 25.6, 25.6, 25.6, 26, 26, 26.2, 26.5, 27, 27.1, 27.4, 27.7, 27.8, 28.2, 28.5, 29, 29.5, 29.5, 30, 30.5, 30.6, 31, 31.1, 31.5, 32, 32.3, 32.6, 33.1, 33.6, 33.7, 34.3, 34.8, 35.2, 35.6, 36.2, 36.4, 37.1, 37.6, 38, 38.6, 39, 39.6, 40, 40.7, 41.2, 41.6, 42.4, 42.8, 43.3, 44.3, 44.7, 45.3, 45.9, 46.7, 47.4, 48, 48.7, 49.3, 50.1, 50.4, 50.8, 51.3, 51.6, 52.1, 52, 52, 52, 52, 51.5, 51.4, 51.1, 51.1, 51, 51, 51, 50.3, 50.3, 50.1, 50, 50, 50, 49, 49, 49, 48.2, 48.2, 48.2, 47.2, 47, 47, 47, 46.1, 46, 46, 46, 45, 45, 45, 44, 44, 44, 43.1, 43, 43, 42.2, 42, 42, 42, 41.3, 41, 41, 40.3, 40.2, 40, 40, 39.2, 39, 39, 39, 38.2, 38, 38, 38, 37.3, 37.5, 38, 38, 38, 38, 38, 38, 38, 38, 38.5, 38.5, 38.5, 38.5, 38.5, 38.5, 38.5, 39, 39, 39, 39, 39, 39, 39.5, 39.5, 39.5, 39.5, 39.5, 39.5, 39.5, 39.5, 39.5, 40, 40, 40, 40, 40, 40, 40, 40, 40, 40, 40.5, 40.5, 40.5, 40.5, 40.5, 40.5, 41, 41, 41, 41, 41, 41, 41, 41, 41, 41, 41, 41 ]
```

See all those decimals in there? That's how little wiggle room is available. a single decimal off, and the contrast will not pass.

Example:

If on your site you have a white background and black text and your brand's color is `#5c403d`, then from that color we get the hue which is 5, and then we go in the array and pick the 6th item (indexes start at 0).
So the link color closer to your brand's color with a saturation of 50% that you can safely use for links is `hsl(5,50%,39.7%)`. Or if you convert it to hex, `#983b33`.

If you don't require AAA conformance and AA is okay, then this array has a minimum contrast of 4.5:1 with both black and white:

```
[ 54, 53, 53, 53, 52, 52, 51, 51, 50, 50, 50, 49, 49, 48, 48, 47, 47, 47, 46, 46, 45, 45, 45, 44, 44, 43, 43, 42, 42, 42, 41, 41, 41, 40, 40, 39, 39, 39, 38, 38, 38, 37, 37, 37, 36, 36, 36, 35, 35, 35, 34, 34, 34, 34, 33, 33, 33, 33, 32, 32, 32, 31, 31, 31, 31, 31, 32, 32, 32, 32, 32, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 33, 34, 34, 34, 34, 34, 34, 34, 34, 34, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 35, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 34, 35, 35, 36, 36, 36, 37, 37, 37, 38, 38, 39, 39, 40, 40, 40, 41, 41, 41, 42, 43, 43, 44, 44, 45, 45, 46, 47, 47, 48, 48, 49, 49, 50, 51, 51, 52, 52, 53, 54, 54, 54, 55, 55, 56, 56, 57, 57, 57, 58, 58, 59, 59, 59, 60, 60, 60, 61, 61, 61, 61, 61, 61, 61, 60, 60, 60, 60, 60, 60, 60, 60, 59, 59, 59, 59, 59, 59, 59, 59, 58, 58, 58, 58, 58, 58, 58, 57, 57, 57, 57, 57, 56, 56, 56, 56, 55, 55, 55, 55, 55, 54, 54, 54, 54, 53, 53, 53, 52, 52, 52, 51, 51, 51, 50, 50, 50, 49, 49, 49, 49, 49, 49, 49, 50, 50, 50, 50, 50, 50, 50, 50, 51, 51, 51, 51, 51, 51, 51, 51, 51, 51, 51, 51, 52, 52, 52, 52, 52, 52, 52, 52, 52, 52, 52, 52, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 53, 54, 54, 54, 54 ]
```

Next up, `hover` and `focus` states: You are required to have a non-color designator for these states.

<span class="test-element">For example try hovering this text</span>
<style>.test-element{color:#3344dd;}.test-element:hover {color:#bb1122;border-top: 1px solid #ddd;border-bottom: 1px solid #ccc;background: #ffffcc;}</style>

The above example uses this CSS for the hover state:


```css
.test-element {
    color: #3344dd;
}
.test-element:hover {
    color: #bb1122;
    border-top: 1px solid #ddd;
    border-bottom: 1px solid #ccc;
    background: #ffffcc;
}
```

There are many methods to differentiate the hover state, the above is a pretty simplistic (and not that pretty) example. But it's important they differ from normal links using something evident and other than their color because not everyone has color vision. It's important to remember that.

If you are building a product like a WordPress theme or plugin or anything where the user has controls to change colors etc, please try to auto-calculate some things. Making the web more accessible starts from the tools we give to our customers. Feel free to use the arrays shared above to make things better for everyone.
