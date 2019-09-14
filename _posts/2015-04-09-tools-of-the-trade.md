---
layout: post
title:  "Tools for developing on WordPress"
permalink: blog/wordpress-dev-tools
categories:
    - WordPress
    - General
---

There are many tools and everyone has their favorites.

I'll just list some of my own favorites here, if some of you haven't heard of them I'd highly recommend you check them out.
First of all though, let me clarify that I do not build websites. My focus is building WordPress plugins and themes that developers can use to build websites for their clients.
That being said, my _favorites_ listed below may not do what you want to do. Still though, I'd recommend you take a look at them. :)

## Server Setup

WAMP, MAMP and the such are all good and get the job done, but they are not **that** good. If you're in the business of developing plugins & themes I'd strongly recommend you switch to [Vagrant](https://www.vagrantup.com/).

Granted, it may not be as simple to install as WAMP/MAMP, 'cause it requires you to do more than just double-click an icon to install it, but it shouldn't take more than 10 minutes to figure it out. Once you setup Vagrant, then a whole new world opens up!

For WordPress I use [VVV](https://github.com/Varying-Vagrant-Vagrants/VVV). It's a pre-configured WordPress stack containing 3 WordPress isntallations:

* Stable WordPress (latest release)
* Trunk (sync'd to the trunk version of WordPress so you can test your products on the next version of WordPress that's currently under development)
* Dev

The 'Dev' site it contains is particularly useful if you contribute to WP Core.

When building something new I always develop on the 'trunk' site, and when I'm working on an existing product I usually work on the stable site and do some additional testing on the trunk site just to be sure that the next WP release won't break anything.

## Code Editor

There are many out there, but I have 2 favorites:

* [Atom](https://atom.io/) (free)
* [Sublime](http://www.sublimetext.com/) (premium)

They are both minimalistic and provide enough tools to make our lives easier, without having all the clutter that full-IDEs have.

## Versioning

I can't stress this enough:
Using a versioning system will greatly improve your development process, assist you in debugging things and finding where something went wrong, and it also helps you collaborate with others.

You can go with SVN, Git or any of the other systems out there.

My favorite is -of course- git.

I use [github](https://github.com/) to host my free - and my premium but still opensource - plugins/themes, and for more sensitive things I use [Bitbucket](https://bitbucket.org/).

Github allows you to have unlimited public repositories (free) and bitbucket does the same for private repositories (free again).

## Pre-Processors

Now that's not something necessary but when writing CSS for my plugins/themes I prefer using a pre-processor. The most famous ones are [LESS](http://lesscss.org/) and [SASS](http://sass-lang.com/). I like them both equally and use them both, depending on the project. The code-editors I mentioned above both have addons available that allow you to compile your stylesheets to normal CSS, and you can even use the command line but I prefer using [CodeKit](https://incident57.com/codekit/). It's not free but it's totally worth it!

## Providing Support

Until recently we were using bbPress for everything on [PressCodes](https://press.codes). We had private forums for our premium products and then public forums for the free products.

However, we recently decided to put most of our products on public repositories on github, no matter if they are free of premium. Our products were always licenced under the GPL, so it made sense to make our codebase public. Instead of selling the code itself, what we're actually selling is support & updates. This presented a new challenge: How can we be sure that a person asking a question has actually bought the product?

We're already using [EDD](https://easydigitaldownloads.com/?ref=98) to sell our products and we use [EDD-Licensing](https://easydigitaldownloads.com/extensions/software-licensing/?ref=98) for our licenses. So the solution we came up with was using HelpScout and the [Help Scout Desk from Sprout Apps](https://sproutapps.co/?ref=180). This plugin allowed us to add a contact form on our site and integrates with HelpScout so that when we get an email there, we can instantly see on the sidebar any active licenses the user has, and their purchase history.

Then we had to make sure that only premium members can see the actual contact form, while others will simply get a message informing them that the support area is only available to premium members. Questions about our free products had to be asked on our free forums.

That took a little custom coding but it was fairly simple so here's the function we wrote:

```php
<?php
/**
 * Uses the_content filter
 *
 * Check if the current user has purchased any current products.
 * If they have, then let them see the rest of the page contents.
 * If not, then show them a hard-coded notice that support is for premium members.
 */
function custom_support_filter_by_client( $content ) {

	// Check if we're on the support page
	if ( is_page( 925881 ) ) {
		global $user_ID;

		$args = array(
			'posts_per_page'   => 999,
			'offset'           => 0,
			'post_type'        => 'download',
			'suppress_filters' => true
		);
		$posts_array = get_posts( $args );

		$is_premium = false;
		foreach ( $posts_array as $post ) {
			$download_id = $post->ID;
			// No need to re-process this if we've already determined that this is a premium customer
			if ( ! $is_premium ) {
				$price = get_post_meta( $download_id, 'edd_price', true );
				// paid product
				if ( 0 < $price ) {
					// Check if the user has purchased this item
					if ( edd_has_user_purchased( $user_ID, $download_id ) ) {
						$is_premium = true;
					}

				}

			}

		}

		if ( ! $is_premium ) {
			// Not a premium customer.
			// Return some content notifying them that this is premium support.

			return '<div style="padding: 15% 0; text-align: center;"><h3>Support tickets are only available to eligible premium products clients.</h3>
			If you seek support for our free products, please use the free support forums instead.</div>';
		}

	}

	// Fallback to returning the default content
	return $content;

}
add_filter( 'the_content', 'custom_support_filter_by_client' );
?>
```

The above will check if the use is currently viewing the support page. If they are, then it will check if the user has purchased any premium products. If they have not, then it will return a custom message and pass it using the `the_content` filter. If they have bought a premium product, then it will simply return the default content of the page which is the contact form.

## Connecting with the team

Staying connected with the rest of the team can be at times a bit daunting.

When it comes to resolving issues we use the issues queue that github provides for our projects. It's an easy way to discuss the problems at hand and find solutions to bugs that are reported by users, or figure out how to implement a feature that we want to include.

Outside github we use [Slack](https://slack.com/) for all our in-team communications. The great thing about Slack is its integrations. We currently have it integrated with github, bbPress (our on-site forums) and HelpScout (our ticketing system).

## Custom Fields

If you want to add custom fields to your post types etc then there are lots of ways to do that! If it's simple fields then you can just cudtom-code them and that's what I usually do.

However, if you want to do things a lot more complex then writing those could potentially be a lot more time-consuming than you can afford. In that case, I prefer using [ACF](http://www.advancedcustomfields.com/). If you buy a developer's licence (which is what I've done) you can also include it in your own plugins (how awesome is that!).


That's my short list and how I handle a few things, I hope you guys find it useful. :)

Cheers!
