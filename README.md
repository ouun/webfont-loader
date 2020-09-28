# Webfonts Loader

Downloads webfonts (like for example Google-Fonts), and hosts them locally on a WordPress site.

This improves performance (fewer requests to multiple top-level domains) and increases privacy. Since fonts get hosted locally on the site, there are no pings to a 3rd-party server to get the webfonts and therefore no tracking.

## Usage

A WordPress theme will typically enqueue assets using the [`wp_enqueue_style`](https://developer.wordpress.org/reference/functions/wp_enqueue_style/) function:

```php
function my_theme_enqueue_assets() {
	// Load the theme stylesheet.
	wp_enqueue_style(
		'my-theme',
		get_stylesheet_directory_uri() . '/style.css',
		array(),
		'1.0'
	);
	// Load the webfont.
	wp_enqueue_style(
		'literata',
		'https://fonts.googleapis.com/css2?family=Literata&display=swap',
		array(),
		'1.0'
	);
}
add_action( 'wp_enqueue_scripts', 'my_theme_enqueue_assets' );
```

To locally host the webfonts, you will first need to download the [`wptt-webfont-loader.php`](https://raw.githubusercontent.com/WPTT/font-loader/master/wptt-webfont-loader.php) file from this repository and copy it in your theme. Once you do that, the above code can be converted to this:
```php
function my_theme_enqueue_assets() {
	// Include the file.
	require_once get_theme_file_path( 'inc/wptt-webfont-loader.php' );
	// Load the theme stylesheet.
	wp_enqueue_style(
		'my-theme',
		get_stylesheet_directory_uri() . '/style.css',
		array(),
		'1.0'
	);
	// Load the webfont.
	wp_add_inline_style(
		'my-theme',
		wptt_get_webfont_styles( 'https://fonts.googleapis.com/css2?family=Literata&display=swap' )
	);
}
add_action( 'wp_enqueue_scripts', 'my_theme_enqueue_assets' );
```

## Supporting IE
The `wptt_get_webfont_styles` will - by default - download `.woff2` files. However, if you need to support IE you will need to use `.woff` files instead. To do that, you can pass `woff` as the 2nd argument in the `wptt_get_webfont_styles` function:
```php
wptt_get_webfont_styles( 'https://fonts.googleapis.com/css2?family=Literata&display=swap', 'woff' );
```

## Storing In A Custom Directory
If you have the need to store font files in a custom directory you can pass a new path through to the `wptt_get_local_fonts_directory_path` filter. The value passed should not include the end folder. If you want to rename the end folder used you can pass a new name through to the `wptt_get_local_fonts_directory_folder` filter. Be sure you add these filters **BEFORE** the file containing the `WPTT_WebFont_Loader` class is called.

```php
// set a new path where the fonts folder will exist/be created.
add_filter( 'wptt_get_local_fonts_directory_path', function() {
	return '/new/path/for/storage';
});

// set a new folder to save fonts in.
add_filter( 'wptt_get_local_fonts_directory_folder' function() {
	return 'new-fonts-folder';
});
```
