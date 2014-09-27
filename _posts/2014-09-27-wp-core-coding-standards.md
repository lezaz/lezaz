---
layout: blog
title:  "WordPress core and coding standards"
permalink: blog/wp-core-coding-standards
---

WordPress is an amazing and brilliant piece of software, no doubt about that!

One thing that WordPress has that other projects lack is [strict coding standards](http://codex.wordpress.org/WordPress_Coding_Standards).  
These coding standards exist for a reason: They help us write better, more readable, concise and well-documented code.  
WordPress core itself is pretty well-documented and someone can easily understand what a function does and how to use it simply by reading its inline documentation and comments.

However WordPress core in a lot of occassions fails to comply to its own coding standards.
Scattered throughout its code you can find code that is not properly indented, missing braces (these actually exist in a lot of files), no spaces after a parenthesis opens or before it closes, trailing spaces, and finally error control operators.  
Though these don't compromise WordPress's integrity or anything else, I believe we should try to "clean up" our code.
The least we can do is make sure that WordPress core complies with the coding standards it proposes!

Starting today I plan to start cleaning up WP Core. The only thing that has kept me from doing it until now is the fact that I despise using SVN (I'm more of a git person).

So... Who's with me? :)
