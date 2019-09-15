---
layout: post
title: "How to only print styles for Gutenberg Blocks used on a page"
permalink: "wordpress/2019/05/11/howto-print-used-blocks-styles"
categories:
  - WordPress
comments_id: 3
---

The new WordPress Editor (codenamed Gutenberg) is amazing as it allows users to build rich and beautiful interfaces with minimal effort. But it comes with a lot of styles and logic bundled-in, and I happen to have strong feelings about that, most of them negative ones. why would anyone enqueue styles for a button block, or a gallery block if a page doesn't use them? We should all strive to build a more sustainable web, so that needs to change.

While building the [Gridd](https://github.com/wplemon/gridd) theme one of the things I was trying to do is minimize the amount of styles that get loaded in a page. That means that I needed a way to find which blocks get used in a page and only print styles for those specific blocks instead of including a large stylesheet including all styles for all blocks like most WordPress themes do.  
Right now (May 2019) the WordPress editor is only used for the main page content, but there are plans to extend that to widgets, headers, footers and everything else a site may include. The easy way to do what I wanted to accomplish would be to just parse `the_content()`, find the blocks used there and print the styles. But that wouldn't be future-proof for when the editor takes-over a large majority of WordPress. So something else had to be implemented.

As with any problem there are many ways to solve this issue, but this is the code I ended up writing. There's [lots more going on in the Gridd theme](https://github.com/wplemon/gridd/blob/v1.1/inc/classes/Scripts.php), but for this article I'll be tweaking it a bit to make things easier to understand:

```php
<?php
/**
 * Enqueue scripts & styles.
 *
 * @package Gridd
 *
 * phpcs:ignoreFile WordPress.Files.FileName
 */

namespace Gridd;

/**
 * Template handler.
 *
 * @since 1.0
 */
class Scripts {

	/**
	 * An array of blocks used in this page.
	 *
	 * @static
	 * @access private
	 * @since 1.0.2
	 * @var array
	 */
	private static $blocks = [];

	/**
	 * Constructor.
	 *
	 * @since 1.0
	 * @access public
	 */
	public function __construct() {
		/**
		 * Use a filter to figure out which blocks are used.
		 * We'll use this to populate the $blocks property of this object
		 * and enque the CSS needed for them.
		 */
		add_filter( 'render_block', [ $this, 'render_block' ], 10, 2 );		
		
		// Add styles in wp_footer.
		add_action( 'wp_footer', [ $this, 'print_late_styles' ] );
	}

	/**
	 * Adds non-critical styles to the footer.
	 *
	 * @access
	 * @since 1.0
	 */
	public function print_late_styles() {
		$blocks = $this->get_blocks();
		if ( empty( $blocks ) ) {
			return;
		}
		echo '<style>';
		foreach ( $blocks as $block ) {
			$block = str_replace( 'core/', '', $block );
			$path  = get_theme_file_path( "assets/css/blocks/$block.min.css" );
			if ( file_exists( $path ) ) {
				include_once $path;
			}
		}
		echo '</style>';
	}

	/**
	 * Filters the content of a single block.
	 *
	 * @since 1.0.2
	 * @access public
	 * @param string $block_content The block content about to be appended.
	 * @param array  $block         The full block, including name and attributes.
	 * @return string               Returns $block_content unaltered.
	 */
	public function render_block( $block_content, $block ) {
		if ( $block['blockName'] ) {
			self::$blocks[] = $block['blockName'];
		}
		return $block_content;
	}

	/**
	 * Get an array of blocks used in this page.
	 *
	 * @access public
	 * @since 1.0.2
	 * @return array
	 */
	public function get_blocks() {
		return array_unique( self::$blocks );
	}
}

/* Omit closing PHP tag to avoid "Headers already sent" issues. */
```

Let's disect this code a bit to see what happens:

In the `__construct()` method, we're adding 2 actions. The 1st one is added in `render_block` and the 2nd one in `wp_footer`.  

The [`render_block` hook](https://developer.wordpress.org/reference/hooks/render_block/) in WordPress 5.0+ can be used to filter the content of a block before rendering it. In this case we're not actually filtering anything, but since this hook runs for each block on the page - no matter where that block is - we'll be using it to figure out which blocks our page includes.  
The 2nd parameter on that hook is an array which contains the block's meta. If you want to see what exactly that includes, you can add something like this in your theme or plugin while developing to see the full array:

```php
add_filter( 'render_block', function( $block_content, $block ) {
	var_dump( $block );
}, 10, 2 );
```

If you do that, you'll notice that one of the items in the `$block` array is `blockName`, with a value like `core/button`. So that's what we're going to use to get the block-types our page includes.  
The `render_block` method in our class simply adds that `blockName` inside a static `$blocks` property in the object. The choice of making this a static property was not random. Static properties are persistent no matter how many instances of the object we have so the array never gets reset. Items are simply getting added as we keep parsing the post-content, widgets, or any other area where blocks may live. Depending on your structure you may be able to use a non-static property instead, in which case instead of `self::$blocks` you'd just use `$this->blocks`. In this case static is just safer.

With the `render_block` filter and method in place, the `$blocks` property of the object now contains an array of all our blocks formatted like this:
```php
[
	'core/button',
	'core/columns',
	'core/button',
	'core/paragraph',
	'core/paragraph'
]
```

Now it's time to actually add the styles for these blocks, and this is where our hook in `wp_footer` comes in.  
Our `wp_footer` hook calls the `print_late_styles` method of our object. Inside that method first we're doing this:
```php
$blocks = $this->get_blocks();`
```
The `get_blocks` method simply returns our blocks with all duplicates removed using the [`array_unique()`](https://www.php.net/manual/en/function.array-unique.php) function. So the example array from above now looks like this:
```php
[
	'core/button',
	'core/columns',
	'core/paragraph'
]
```

Next, the `print_late_styles` method does this:
```php
if ( empty( $blocks ) ) {
	return;
}
echo '<style>';
foreach ( $blocks as $block ) {
	$block = str_replace( 'core/', '', $block );
	$path  = get_theme_file_path( "assets/css/blocks/$block.min.css" );
	if ( file_exists( $path ) ) {
		include_once $path;
	}
}
echo '</style>';
```
First, we add a check to see if we actually have blocks. There's no reason to print any styles if we don't, so if we don't have any blocks we simply exit early.  
Next, we open a `<style>` tag and inside it add a loop to go through all our blocks. What you do inside that loop is up to you, but the Gridd theme has a folder in [`assets/css/blocks`](https://github.com/wplemon/gridd/tree/v1.1/assets/css/blocks) where there's a separate file for each block. So what we do here is simply remove the `core/` part of the block's name, and then if we have a stylesheet for that block, include it.

And that's all there is to it!

Now I know some of you may be asking yourselves _why do this in `wp_footer`? Why would you print styles in the footer instead of the header?_  
There are many reasons I chose to do it there, and it was a consious choice. As I mentioned in the beginning, WordPress is evolving. Right now blocks only live in the content, but that won't be the case forever. Sure, you could do it in `wp_head` instead, and parse the content from the main `$query`. But what happens when widgets are also blocks? How will you get the content of all your widgets from all your sidebars, figure out what blocks they contains and add their styles in `wp_head` before the page is even parsed by WP? There are ways, but they are over-complicated for no good reason. There really is no good reason to force styles to be in `<head>` other than the fact that we're used to doing things that way.  
Additionally, by reducing the amount of styles in `<head>` the browser can start rendering the page sooner, thus further improving the page performance. Block styles are not **critical** styles, your content can live without them just fine for a couple of milliseconds while the page is loading. The only case your readers won't notice a difference is if they are on a really slow 2G network using a 10-year old phone. But even then they will be thankful since they'll be able to start digesting your content without having to wait any longer than it's absolutely necessary.

I'll be closing this post with some general thoughts/advice:  
Be thoughtful. Try to make the web more sustainable and accessible for everyone. Don't assume that the rest of the world is on a fast network on a state-of-the-art device. Be a good sport and help us all make the web a better place for our future. As developers it is our responsibility to try and fix things that are wrong with the web and the way we use it.