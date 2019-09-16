---
layout: post
title: "Typography Scales with CSS Variables"
permalink: "blog/typography-scales-with-css-vars"
categories:
  - CSS
comments_id: 8
---

A typography scape makes all our typography sizes relative to one another. These 2 quotes from the [IBM Design Language](https://www.ibm.com/design/v1/language/experience/visual/typography/) site pretty much convey why a typography scale is useful:

> Scale creates consistency in sizing across elements. A visual type scale turns a typographic balancing act into a set ratio. Use a type scale based on your users and their content.

> **Type scales help users quickly skim and scan**
>
> By using a type scale, you fulfill users’ expectations by giving order to how things should be communicated and how they should appear. A modular scale is a design tool, but not a guarantee. Good type application still relies on designers to apply aesthetic sensibility to their overall layout in a way that addresses user needs.

So how can you do that on your site? Well, you can use the method below:

First I like defining some variables that I can use throughout the site:
```css
:root {
  --scale: 1.333;
  /* Don't change the lines below, you only need to change the --scale above. */
  --size-l-1: calc(1rem * var(--scale));
  --size-l-2: calc(var(--size-l-1) * var(--scale));
  --size-l-3: calc(var(--size-l-2) * var(--scale));
  --size-l-4: calc(var(--size-l-3) * var(--scale));
  --size-l-5: calc(var(--size-l-4) * var(--scale));
  --size-l-6: calc(var(--size-l-5) * var(--scale));
  --size-l-7: calc(var(--size-l-6) * var(--scale));
  --size-s-1: calc(1rem / var(--scale));
  --size-s-2: calc(var(--size-s-1) / var(--scale));
  --size-s-3: calc(var(--size-s-2) / var(--scale));
}
```
The variables names vary depending on the project, but in the above example I named the variables using a pattern
```
--size-{large/small}-{multiplier}
```
So `--size-l-1` is `1rem` multiplied by our defined scale (the `--scale` var). `--size-l-2` is the `--size-l-1` var multiplied by `--scale`. Or if you prefer, `1em` multiplied 2 times by our `--scale` and so on. The `--size-s-1` is our base font-size (1rem) divided by our scale 1 time, `--size-s-2` is divided twice and so on.

Now that we have the sizes properly defined and calculated we can just use them in our styles:

```css
h1 {
  font-size: var(--size-l-5);
}
h2 {
  font-size: var(--size-l-4);
}
h3 {
  font-size: var(--size-l-3);
}
h4 {
  font-size: var(--size-l-2);
}
h5 {
  font-size: var(--size-l-1);
}
h6 {
  font-size: 1rem;
}
.large {
  font-size: var(--size-l-1)
}
.larger {
  font-size: var(--size-l-2)
}
.largest {
  font-size: var(--size-l-3)
}
small, .small {
  font-size: var(--size-s-1)
}
.smaller {
  font-size: var(--size-s-2)
}
.smallest {
  font-size: var(--size-s-3)
}
```

You can also combine this with a [Fluid Typography](/blog-fluid-typography-css-vars) implementation for better results on all screen-sizes.

## Alternative solutions

If you don't want to use css-variables, then you can hardcode these values. To get the values without doing all the math you can use a tool like [Visual Typography Scales](https://type-scale.com/), and then define your values using `rem` units.

## If you're building WordPress Themes

The method described above is excellent for use in WordPress themes. Themes don't need to include a separate control for H1, H2, H3 etc sizes. All you need is a slider control to let users define the scale. Take a lookat the [Gridd](https://wordpress.org/themes/gridd) for an example.