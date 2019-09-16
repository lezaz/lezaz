---
layout: post
title: "Styling Links"
permalink: "blog/styling-links"
categories:
  - CSS
comments_id: 6
---

Links are a part of any page. They have been a part of the web from its conception, and are the main way we navigate on our everyday lives. They're everywhere! So it stands to reason you should style them appropriately.

Links should not blend in with their surrounding text. They need to be seen, they need to be different. If they're not, it will be difficult to find them. Design should make our lives easier. So here are some rules you should follow:

* Don't remove the `underline` from links. If you remove it, replace it with something equally visible (like for example `border-bottom`).
* If there is no `underline`, `border-bottom` or `box-shadow` in your links, make sure that there is sufficient contrast between the colors you have selected. And by "sufficient contrast" I mean you should follow the WCAG guidelines: `7:1` contrast with the background, and `3:1` contrast with surrounding text. Lea Verou has built an amazing tool you can use to check these on [contrast-ratio.com](https://contrast-ratio.com/). Use it. These numbers were not just picked at random, there are real valid reasons why you should follow these guidelines. And it's not just for "accessibility", it's to **make things easy for everyone**.
* `:hover` should have different styling. It doesn't need to be extremely different, but enough to indicate the user is hovering the link.
* `:focus` and `active` should be properly indicated. By default browsers use an `outline` for these statuses, if you remove it then make sure you replace it with something else.

Personally I don't like using many colors, so I use something similar to this:

```css
a {
  font-weight: 600;
  text-decoration: none;
  border-bottom: .1em solid;
}

a:hover,
a:focus,
a:active {
  box-shadow: 0 .1em;
}

a:focus,
a:active {
  outline: .1em solid;
}
```

Breaking down the above code:

Since I don't use a separate color for links, I'm adding some default styles to them that makes them look different and stand out.  

In CSS we can't control the thickness of the default undersline, so I chose to remove that and instead use `border-bottom` with a value of `.1em solid`. That makes the underline scale depending on the font-size, so headers have a thicker border than the main text. Additionally you can change the color of borders, so that provides more design options.

-----------------

On `:hover`, `:focus` and `:active` I'm adding a `box-shadow` with a value `0 .1em`. The box-shadow will sit right below the bottom border making it look a bit thicker. The reason I'm using `box-shadow` instead of just changing the value of `border-bottom` is because now I have the freedom to use a slightly different shade of the color I used for the border., which looks extremely well if you choose the right colors.  

------------------
Finally on `:focus` and `:active` I'm adding an `outline` of `.1em solid`. This way keyboard navigation is easier because it's always clear where users using a keyboard instead of a mouse are.

-----------------
There are many ways you can handle styling links. As long as they are styled differently, are visible, and theis states are sufficiently different you'll be OK. But **do not, under any circumstances** make them the same as your normal text. Doing so is just annoying and breaks the way we expect things to work. Not to mention it's just an inconsiderate dick move for accessibility. You're breaking the web.