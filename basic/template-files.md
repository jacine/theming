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

## Overriding Template Files

The template files provided by Drupal core and contributed modules represent the default markup implementation chosen by the original author or team, but every last one of these template files and the markup and variables printed inside of them is customizable. When developing a theme, if you decide the default implementation is not going to suit your needs, you can simply choose to override it. Drupal's theme layer is designed to be extremely flexible and easy to manipulate in this way.

The beauty of theming Drupal sites is that you can easily make changes without having to modify templates where they originate. The process of overriding template files is extremely simple:

1. Find the original template file by browsing through code or checking http://api.drupal.org.
2. Copy and paste it into your theme directory.
3. Clear the site cache and reload!

After following these three steps, Drupal will begin using the theme's version of the file, and you are free to make whatever changes you wish. It's that simple.

<blockquote><b>Tip:</b> A quick way to ensure that Drupal is using the template file you've just overridden in your theme is to add text to the top of the template file, like "Hello World". If your text appears when you reload, you'll know you're working with the correct file.</blockquote>

## Global Template Variables    

Template files usually contain a few more variables than they actually print. In some cases there are many more. This is a great thing for theme developers because it opens up many possibilities for manipulating the display of markup without the need for much PHP knowledge. Table 15-2 describes some of the helpful variables available in all templates (with the exception of the attribute variables; these are covered section the "HTML Attributes" section). Identifying available variables is covered in detail in the next chapter.

**Table 15-2**. Variables available in all templates

Variable | Description
:------- | :----------
`$is_admin` | Helper variable that equals TRUE if the currently logged in user is an administrator, and FALSE otherwise.
`$logged_in` | Helper variable that equals TRUE if the current user is logged in, and FALSE otherwise. The `$user->uid` is used to determine this information, as anonymous users always have a user ID of 0.
`$is_front` | Helper variable that uses the `drupal_is_front_page()` function to determine if the current page is the front page of the site. Equals TRUE on the front page (unless the database is offline), and FALSE otherwise.
`$directory` | The directory in which the template being used is located.
`$user` | An object that contains account information of the currently logged in user. It may be accessed by adding the line `global $user;` to the template you are working in. Never print any properties of it directly because it contains raw user data and thus it is insecure. Instead, use `theme_username();` for example, `theme('username', array('account' => $user))`.
`$language` | An object that contains information about the language currently being used on the site, such as `$language->dir`, which contains the text direction, and `$language->language` which would contain `en` for English. It may be accessed by adding the line `global $language;` to the template you are working in.
`$theme_hook_suggestions` | An array containing other possible theme hooks, which can be used as variants for naming template files and theme functions or to determine context. See the "Theme Hook Suggestions" section.
`$title_prefix` and `$title_suffix` | Render arrays containing elements, such as contextual links, to be printed before and after the title in templates or at the top and bottom of template files where a title does not exist.

### HTML Attributes

In Drupal 7, we began storing attributes in arrays. Part of the reason this was done is the RDF module. The RDF module utilizes these variables to tack on its data during the preprocess phase. Another reason was to allow theme developers more control over the classes printing out in their template files in preprocess functions.

Each of these variables, described in Table 15–3, has an array and string version. The array version, which contains the suffix `_array` in the variable name, is populated during various preprocess functions, such as `template_preprocess()` and `template_preprocess_node()` or `template_preprocess_block()`. Then, during the `template_process()` phase, new variables containing a flattened or string version of these arrays is created for use in templates. This process is illustrated in Figure 15–15. See the "Preprocess and Process Functions" section of this chapter for more details.

**Table 15–3**. Pluggable HTML Attributes

Variable | Description
:------- | :----------
`$attributes` | Contains HTML attributes provided by modules (mainly RDF), except for the class attribute, which is handled separately (see below). `$attributes`, available as `$attributes_array` in preprocess, is usually reserved for the top-level HTML wrapper element, such as `<body>` or outermost `<div>` in other template files.
`$classes` | Contains HTML classes for templates. Usually reserved for the top-level HTML wrapper element, such as `<body>` or outermost `<div>` in other template files.
`$title_attributes` | Contains classes for the top-level heading, such as a node or block title, of the template file, which is usually an `<h2>` for node teaser or block content.
`$content_attributes` | Contains classes for the content wrapper `<div>`, or post body of templates. An example of how these variables are used can be found in the `node.tpl.php` file.

![Screenshot of node.tpl.php source with notes and screenshots of array output for pluggable attributes](http://themery.com/sites/default/files/figure-15-15.png)
**Figure 15–15.** Excerpt from `node.tpl.php`, which highlights how the pluggable HTML attributes are used

<blockquote><b>Tip:</b> If you don't see these attributes in your source code, be sure to enable the RDF module.</blockquote>

All of the common core templates provide detailed documentation of the available variables. A quick look at the default `block.tpl.php` template file, located in the `modules/block` directory reveals that most of the contents of the file is actually documentation for the available variables. As shown in Listing 15–8, you can get a good idea of what you have to work with by just looking at the documentation and code.

**Listing 15–8**. Source Code for Default `modules/block/block.tpl.php`, Including Variable Documentation

```html
/**
 * @file
 * Default theme implementation to display a block.
 *
 * Available variables:
 * - $block->subject: Block title.
 * - $content: Block content.
 * - $block->module: Module that generated the block.
 * - $block->delta: An ID for the block, unique within each module.
 * - $block->region: The block region embedding the current block.
 * - $classes: String of classes that can be used to style contextually through
 *   CSS. It can be manipulated through the variable $classes_array from
 *   preprocess functions. The default values can be one or more of the
 *   following:
 *   - block: The current template type, i.e., "theming hook".
 *   - block-[module]: The module generating the block. For example, the user
 *     module is responsible for handling the default user navigation block. In
 *     that case the class would be 'block-user'.
 * - $title_prefix (array): An array containing additional output populated by
 *   modules, intended to be displayed in front of the main title tag that
 *   appears in the template.
 * - $title_suffix (array): An array containing additional output populated by
 *   modules, intended to be displayed after the main title tag that appears in
 *   the template.
 *
 * Helper variables:
 * - $classes_array: Array of html class attribute values. It is flattened
 *   into a string within the variable $classes.
 * - $block_zebra: Outputs 'odd' and 'even' dependent on each block region.
 * - $zebra: Same output as $block_zebra but independent of any block region.
 * - $block_id: Counter dependent on each block region.
 * - $id: Same output as $block_id but independent of any block region.
 * - $is_front: Flags true when presented in the front page.
 * - $logged_in: Flags true when the current user is a logged-in member.
 * - $is_admin: Flags true when the current user is an administrator.
 * - $block_html_id: A valid HTML ID and guaranteed unique.
 *
 * @see template_preprocess()
 * @see template_preprocess_block()
 * @see template_process()
 *
 * @ingroup themeable
 */
?>
<div id="<?php print $block_html_id; ?>" class="<?php print $classes; ?>"<?php print $attributes; ?>>
  <?php print render($title_prefix); ?>
  <?php if ($block->subject): ?>
    <h2<?php print $title_attributes; ?>><?php print $block->subject ?></h2>
  <?php endif;?>
  <?php print render($title_suffix); ?>
  <div class="content"<?php print $content_attributes; ?>>
    <?php print $content ?>
  </div>
</div>
```

At the top of the file there is a `@file` block, which briefly describes the purpose of the file. Underneath, there is a long list of variables, some of which are printed in the template file and some that are not. There are also `@see` references to applicable preprocess and process functions, which are discussed in more detail in the next chapter.

To get an up-close idea of what this template file produces, take a look at a block produced by the Bartik theme. Bartik does not include a `block.tpl.php` file; it uses Drupal's default, which is provided by the Block module. Create a custom block with the title "My Custom Block" and some dummy text as the body, and place it in the Sidebar First region of the Bartik theme.

![Screenshot of block configuration form](http://themery.com/sites/default/files/figure-15-16.png)

**Figure 15–16.** Screenshots of our rendered custom block as viewed using the Bartik theme and the configuration page for the block

Your custom block, shown in Figure 15–16 along with the `block.tpl.php` template file in Listing 15–8, produces the output displayed in Listing 15–9 for anonymous users. The block title is printed by `<?php print $block->subject ?>` and the body is printed by `<?php print $content ?>`. Drupal will only populate variables and display content that the user viewing it has access to.

**Listing 15–9**. HTML output of a custom block titled "My Custom Block" when logged out.

```html
<div id="block-block-1" class="block block-block">
  <h2>My Custom Block</h2>
  <div class="content">
    <p>Enim quam iusto quam iis enim. Molestie at et diam ut legere. Feugiat tation facilisis quarta soluta quam. Facilisis lectorum modo nam modo suscipit.</p>
  </div>
</div>
```

Listing 15–10 shows the HTML for the same block as it is displayed to users logged in as administrators. You'll notice that the code is different. Administrators have access to contextual administrative links, added by the Contextual Links module. These links are printed via the `<?php print render($title_prefix); ?>` line. The Contextual Links module also adds a class to the wrapper `<div>` identifying it as a contextual-links-region. This behavior is not specific to the Block module or the `block.tpl.php` template file. The `$title_prefix` and `$title_suffix` variables were created to allow modules to inject content before and after titles in template files, which the Contextual links module takes advantage of.

**Listing 15–10**. HTML output of a custom block titled "My Custom Block" when logged in as an administrator, highlighting the output of `$title_suffix`.

```html
<div id="block-block-1" class="block block-block contextual-links-region">
  <h2>My Custom Block</h2>
  <div class="contextual-links-wrapper contextual-links-processed">
    <a class="contextual-links-trigger" href="#">Configure</a>
    <ul class="contextual-links">
      <li class="block-configure first last"><a href="/admin/structure/block/manage/block/1/configure?destination=node">Configure block</a></li>
    </ul>
  </div>
  <div class="content">
    <p>Enim quam iusto quam iis enim. Molestie at et diam ut legere. Feugiat tation facilisis quarta soluta quam. Facilisis lectorum modo nam modo suscipit.</p>
  </div>
</div>
```
