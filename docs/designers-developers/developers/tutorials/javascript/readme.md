
# Getting Started with JavaScript

The purpose of this tutorial is to step through getting started with JavaScript and WordPress. The Gutenberg documentation contains information on the APIs available, but as a reference and not necessarily full working examples. The goal of this tutorial is get you comfortable on how to use these snippets of code.


## Plugins Background

The primary means of extending WordPress remains the plugin, see [Plugin Basics](https://developer.wordpress.org/plugins/the-basics/) for more. The quick start is create a new directory in `wp-content/plugins/` to hold your plugin code, call it `my-plugin`.

Inside of this new directory, create a file called `my-plugin.php` which will be the server-side code the runs when your plugin is active. For now you can just place the following in that file:

```php
<?php
/*
Plugin Name: My Plugin
*/
```

So you should have a directory `wp-content/plugins/my-plugin/` which has the single file `my-plugin.php`. Once that is in place, you can go to your plugins list in `wp-admin` and you should see your plugin listed.

Click **Activate** and your plugin will load with WordPress.


## Loading JavaScript

Now with the plugin in place, we can add our code that loads the JavaScript we want to use. This follows the standard WordPress methodology of enqueuing your scripts, see [Including CSS & JavaScript](https://developer.wordpress.org/themes/basics/including-css-javascript/) for more details.

Add the following code to your `my-plugin.php` file:

```php
add_action( 'admin_enqueue_scripts', 'my_enqueue' );
function my_enqueue( $hook ) {
	wp_enqueue_script( 'my-plugin',
		plugins_url( 'my-plugin.js', __FILE__ ),
		array( 'wp-blocks', 'wp-element' )
	);
}
```

Create a file called `my-plugin.js` and add:

```js
console.log( "I'm loaded!" );
```

Now go to any page within `/wp-admin/` and check your browser Developer Tools, and you should see the message in your console log. See [Mozilla's What are browser developer tools?](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools) if you need more information, the area to become most familiar with is [The JavaScript console](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools#The_JavaScript_console).

The script enqueuing used the `admin_enqueue_scripts` hook which only loads the JavaScript within the wp-admin section. So if you navigate to any front-end page or article on your site, you will not see the message.

**Note:** The last argument in the `wp_enqueue_script()` function is an array of dependencies, all of the Gutenberg packages are registered and can be loaded by specifying in the array, blocks and elements are shown as two common examples.

Recap at this point, we have a plugin which loads JavaScript, we're off to a good start.


## JavaScript versions and building

The Gutenberg Handbook shows examples in two syntaxes ES5 and ESNext, note ESNext is sometimes referred to as ES6. The ES5 code is compatible with almost all current browsers.

The ESNext syntax is compatible with most modern browsers, but unfortunately not IE11. Additionally, the ESNext code examples include JSX syntax, which requires a build step using webpack to transform into code that can be run in your browser.

For this tutorial, all examples will be in straight ES5 code that can be run straight in your browser and does not require an additional build step.


## Extending the Block Editor

This puts all the initial pieces in place for you to start extending the Block Editor.

Let's look at using the [Block Style Variation example](../../../../../docs/designers-developers/developers/filters/block-filters/#block-style-variations).

Update the file `my-plugin.js` with:

```js
wp.blocks.registerBlockStyle( 'core/quote', {
    name: 'fancy-quote',
    label: 'Fancy Quote'
} );
```

You can remove the previous `console.log` if you like.

After you add the code, save the file, and start a new post in the Block Editor.

Add a quote block, and in the right sidebar under Styles, you should see your new Fancy Quote style listed. You can go back to the JavaScript and change the label to "Fancy Pants" and reload, and you should see the new label.

Previewing or Publishing the post, you will not see a visible change. However, if you look at the source, you should see `is-style-fancy-quote` class name attached to your quote.

You could create a `style.css` file with:

```css
.is-style-fancy-quote {
	font-size: 64px;
}

```

and enqueue the CSS by adding the following to your `my-plugin.php`:

```php
	function my_stylesheet() {
		wp_enqueue_style( 'my-style', plugins_url( 'style.css', __FILE__ ) );
	}
	add_action( 'wp_enqueue_scripts', 'my_stylesheet' );
```

And then when you view the page, you should see it in a very large font.


