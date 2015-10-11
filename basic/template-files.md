# Template Files

Drupal core, its modules, and contributed modules provide much of their output in the form of template files. Template files consist of HTML markup and PHP variables. This makes it fairly easy for those with little or no PHP experience to make changes to HTML code.

A simple example of a template file is `user-picture.tpl.php` (see Listing 15–7). This template is located in the `modules/user` directory and its sole purpose is to print a site user's picture as either an image or an image with a link (depending on whether or not the user viewing the photo has access to view user profiles). It wraps the picture in a `<div class="user-picture">`. This template file will be used anywhere the `user_picture` theme hook is called, such as the user profile page and author information for nodes and comment (where enabled).

**Listing 15–7**. Contents of `user-picture.tpl.php` file.

```html
<?php
/**
 * @file
 * Default theme implementation to present a picture configured for the
 * user's account.
 *
 * Available variables:
 * - $user_picture: Image set by the user or the site's default. Will be linked
 * depending on the viewer's permission to view the users profile page.
 * - $account: Array of account information. Potentially unsafe. Be sure to
 * check_plain() before use.
 *
 * @see template_preprocess_user_picture()
 */
?>
<?php if ($user_picture): ?> 
  <div class="user-picture">
    <?php print $user_picture; ?>
  </div>
<?php endif; ?>
```

A typical page on a Drupal site is essentially a big tree of nested template files and theme functions. As Figure 15–14 illustrates, this tree begins with larger templates such as `html.tpl.php` and `page.tpl.php` files and goes all the way down `field.tpl.php`, which is used to print fields.

![An illustrated screenshot of the Bartik theme which exposes the use of the major templates](http://themery.com/sites/default/files/figure-15-14.png)

**Figure 15–14**. An example home page using the Bartik theme, which highlights the use of major template files and many custom regions.

## Common Core Templates

Drupal core contains over forty template files, but there are six major template files (described in Table 15–1) that are tasked with making up the majority of each page. These major template files are the ones you'll be working with most when writing Drupal themes and they will allow you do most of the heavy lifting in your theme.

Table 15–1. Common Core Template Files

Name | Origin | Purpose
:--- | :----- | :------
`html.tpl.php` | `modules/system` | Prints the structure of the HTML document, including the contents of `<head>` tags, e.g. $scripts, and $styles, as well as opening and closing `<body>` tags with `$page_top`, `$page` and `$page_bottom` regions printed inside. Unless you need to change the DOCTYPE, there's probably no reason to override this file.
`page.tpl.php` | `modules/system` | Prints the page level regions and other hard-coded variables such as `$logo`, `$site_name`, `$tabs`, `$main_menu`, etc. Full control of the site layout is possible by manipulating this file, and most themes provide their own version of it.
`region.tpl.php` | `modules/system` | Prints the HTML markup for regions.
`block.tpl.php` | `modules/block` | Prints the HTML markup for blocks.
`node.tpl.php` | `modules/node` | Prints the HTML markup for nodes.
`comment.tpl.php` | `modules/comment` | Prints the HTML markup for comments.
`field.tpl.php` | `modules/field/theme`* | Prints the HTML markup for fields. There are many different types of fields, and since this file needs to cover every case, its implementation is very general. If having semantic markup is important to you, you'll probably end up with a few versions of this template.

<small>*<code>field.tpl.php</code> is used only when overridden by a theme. The one in <code>modules/field/theme</code> is only provided as a base for your work.</small>
