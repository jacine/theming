# Theme Functions

The purpose of a theme function is the same as a template file in that its goal is to provide HTML markup in a way that makes it customizable by themes (and modules, too). There are many, many theme functions in Drupal core, from form elements to menu items to full administration page implementations. For a full list of theme functions available in Drupal 7, visit http://api.drupal.org/api/group/themeable/7.

## How Theme Functions Are Created    

Drupal core and modules usually define theme functions, but they can be defined by themes as well. hook_theme() implementations are where all the juicy information about most generic theme functions resides, including what parameters these functions accept. Theme hooks are covered in detail in the “Theme Hook Suggestions" section later in this chapter, but Listing 15–11 shows what a simple hook_theme() implementation looks like.

**Listing 15–11**. Example `hook_theme()` implementation.

```html
<?php
/**
 * Implements hook_theme().
 */
function mymodule_theme() {
  return array(
    'my_theme_hook' => array(
      'variables' => array('parameter' => NULL),
    ),
  );
}
```

Implementations of `hook_theme()` let Drupal know about theme hooks. Once Drupal is aware, it will search for a theme function called `theme_my_theme_hook()` in this case, which might look like the code in Listing 15–12.

**Listing 15–12**. Example theme function implementation.

```html
<?php
function theme_my_theme_hook($variables) {
  $parameter = $variables['parameter']; 
  if (!empty($parameter)) {
    return '<div class="my-theme-hook">' . $parameter . '</div>';
  }
}
```

## Calling Theme Functions    

Throughout this chapter we refer to theme functions as `theme_this()` and `theme_that()`. That's what the functions are named and usually referred to as. However, you should never call a theme function directly. Doing so will reverse the wonderful functionality that comes along with Drupal's theme layer, such as overrides, suggestions, etc. Always use the `theme()` function to generate theme output. It takes care of routing the request to the appropriate theme function. For more information on how this works, see http://api.drupal.org/api/function/theme/7.

Using `theme_image()`, Listings 15–13 and 15–14 illustrate the right and wrong way to call theme functions, respectively.

**Listing 15–13**. The CORRECT way to call a theme function.

```html
<?php print theme('image', array('path' => 'path/to/image.png', 'alt' => 'Image description')); ?>
```

**Listing 15–14.** The WRONG way to call a theme function.

```html
<?php print theme_image(array('path' => 'path/to/image.png', 'alt' => 'Image description')); ?>
```

## Overriding Theme Functions    

Overriding theme functions is very similar to overriding template files. The main difference is that you are working with functions, and your overridden theme functions all reside in `template.php`. The steps involved in overriding a theme function are as follows:

1. Find the original theme function by browsing through Drupal's source code or checking http://api.drupal.org.
2. Copy and paste it into your `template.php` file.
3. Change the beginning of the function name from `theme_` to `yourthemename_`.
4. Save `template.php`, clear the site cache, and reload!

<blockquote><b>Caution:</b> If creating <code>template.php</code> from scratch, remember to include <code>&lt;?php</code> at the top of the file. Also note that a closing tag should not be added at the bottom of the file. Omitting the closing PHP tag prevents unwanted whitespace, which can cause "Cannot modify header information" or "Headers already sent" errors. For more information, visit <a href="http://drupal.org/node/1424">http://drupal.org/node/1424</a>.</blockquote>

## Exercise: Let's Override a Theme Function

Here is a theme function called `theme_more_link()`. It is used to print a link to additional content in blocks. To find the code for the theme function, take a look at http://api.drupal.org/api/function/theme_more_link/7.

**Step 1**. Copy and paste the original theme function code into `template.php`.

```html
<?php
/**
 * Returns HTML for a "more" link, like those used in blocks.
 *
 * @param $variables
 * An associative array containing:
 * - url: The url of the main page.
 * - title: A descriptive verb for the link, like 'Read more'.
 */
function theme_more_link($variables) {
  return '<div class="more-link">' . l(t('More'), $variables['url'], array('attributes' => array('title' => $variables['title']))) . '</div>';
}
```

**Step 2**. Change the beginning of the function name to your theme's name, save it, and clear the site cache.

```html
<?php
/**
 * Returns HTML for a "more" link, like those used in blocks.
 *
 * @param $variables
 * An associative array containing:
 * - url: The url of the main page.
 * - title: A descriptive verb for the link, like 'Read more'.
 */
function dgd7_more_link($variables) {
  return '<div class="more-link">' . l(t('More'), $variables['url'], array('attributes' => array('title' => $variables['title']))) . '</div>';
}
```

**Step 3**: Drupal will now use your version of the theme function, so make changes!

```html
<?php
/**
 * Overrides theme_more_link().
 * - Changed the text from "More" to "Show me More"
 * - Changed the class from "more-link" to "more"
 */
function dgd7_more_link($variables) {
  return '<div class="more">' . l(t('Show me MORE!'), $variables['url'], array('attributes' => array('title' => $variables['title']))) . '</div>';
}
```

<blockquote><b>Tip</b> In Step 3, you'll notice that the comment block has been changed to indicate what function was overridden and the changes that were made. Documenting your code is always a good idea, and explicitly listing the reasons why you've overridden a theme function can be a big time saver in the future. Theme functions change, and some aren't as small as a few lines. When upgrading major versions of Drupal, such as Drupal 7 to Drupal 8, such comments will make your life a lot easier.</blockquote>
