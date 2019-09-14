---
layout: post
title:  "How to control content access using Easy Digital Downloads"
permalink: blog/edd-control-content
categories:
    - Easy-Digital-Downloads
    - WordPress
---

I recently needed to control access to a certain page on my site using [Easy Digital Downloads](https://easydigitaldownloads.com/?ref=98). My use-case was pretty specific: I have a support page on [PressCodes](https://press.codes/support/) where clients that have purchased a premium product can submit their question in a form.
If a visitor has not purchased a premium product, they simply get a message informing them they don't have access to that page.

There are lots of products on that site. Some of them are free, others are premium. So we needed to write a script that will check if the current user has purchased any products with a price greater than 0. If they have then they are considered a _premium_ client and they can see the content.

```php
<?php

/**
 * Uses the_content filter
 *
 * Check if the current user has purchased any premium products.
 * If they have, then let them see the rest of the page contents.
 * If not, then show them a hard-coded notice that support is for premium members.
 */
function custom_support_filter_by_client( $content ) {

    // The page we want to control
    $page_id = 925881;

    // The message that will be displayed to non-premium users
    $message  = '<div style="padding: 15% 0; text-align: center;">';
    $message .= '<h3>Message title</h3>';
    $message .= '<p>Message content</p>';
    $message .= '</div>';

	// Check if we're on the support page
	if ( is_page( $page_id ) ) {
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
			// No need to re-process this
            // if we've already determined that this is a premium customer
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
			return $message;
		}

	}

	// Fallback to returning the default content
	return $content;

}
add_filter( 'the_content', 'custom_support_filter_by_client' );
?>
```

The above code is pretty simple and just goes through the user's purchases when they visit that page. It then uses the `the_content` filter to change the content of the `$page_id` we have defined, but you could use whatever suits your use-case.
It would be fairly simply to modify it to check for example for a specific download by changing the query arguments, or extend it to accommodate your own custom logic. :)
