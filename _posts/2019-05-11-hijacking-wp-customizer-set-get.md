---
layout: post
title: "Hijacking the wp.customize set & get methods for settings"
permalink: "wordpress/customizer/2019/05/11/hijacking-wp-customizer-set-get"
categories:
  - WordPress
  - Customizer
comments_id: 4
---

While refactoring the [Kirki framework](https://aristath.github.io/kirki) for v4.0, I needed to hijack the `get` and `set` functions from the JS API in order to apply some custom logic, so this is just a quick post to show how you can do that.  
Create and enqueue a JS file in the customizer, and add this code in there:

```js
( function( api ) {

	/**
	 * Set the value and trigger all bound callbacks.
	 *
	 * @since 1.0
	 * @param {object} to New value.
	 */
	api.Value.prototype.set = function( to ) {
		var from = this._value,
		parentSetting,
		newVal;

		to = this._setter.apply( this, arguments );
		to = this.validate( to );

		// Bail if the sanitized value is null or unchanged.
		if ( null === to || _.isEqual( from, to ) ) {
			return this;
		}

		this._value = to;
		this._dirty = true;

		this.callbacks.fireWith( this, [ to, from ] );

		return this;
	};

	/**
	 * Get the value.
	 *
	 * @return {mixed}
	 */
	api.Value.prototype.get = function() {
		return this._value;
	};
} ( wp.customize ) );
```

The code above is a verbatim copy from the core WP files, so you can just tweak them to your liking, add hooks using the new [`wp.hooks`](https://wordpress.org/gutenberg/handbook/designers-developers/developers/packages/packages-hooks/) package etc. For an example of what Kirki does, you can [check out the code here](https://github.com/kirki-framework/control-base/blob/63b20a3ac054bb6cdbe9a5e1825039f75b239418/src/assets/scripts/dynamic-control.js#L208-L271).