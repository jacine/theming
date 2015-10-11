# Theme Hooks and Theme Hook Suggestions


Theme functions and templates are defined by theme hooks. By making use of theme hook suggestions, you have a lot more flexibility to override theme functions or templates in certain situations. This section covers both ways to greatly increase the power and maneuverability of your custom theme.

## What is a Theme Hook?    

In Drupal, theme hooks refer to template files and functions that have been specifically registered via `hook_theme()`. This may sound scary or over-technical to non-PHP developers, but honestly it's not. You've already learned about template files and theme functions, so technically you already have a pretty good grasp on theme hooks.

Whether a template file or function is implemented in core is decided on a case-by-case basis, and the criteria for making this decision is usually a balance between how likely it is to be reused by other modules, how often it is expected to change, and whether or not it makes sense for performance reasons. Template files are slightly slower than theme functions so they are not always desirable. Smaller bits of markup for things like form input elements are more efficiently implemented as theme functions, whereas larger chunks like nodes and blocks are better as a template file.

Both theme functions and template files exist as a way for Drupal and its modules to create output consisting of markup and variables in a way that you, the theme developer, can override and make it your own. They are both entirely YOUR domain, and you get the last word as to how they should look.

Both share the same exact theme hook. For example, a template file called `node.tpl.php` and a function called `theme_node()` share the same node theme hook. The difference is in the implementation, as both cannot be used at the same time.

Both can take advantage of preprocess functions, which allow you to intercept and alter variables before rendering. Using the node hook as an example, this would look like `template_preprocess_node()` in your theme it would be `yourtheme_preprocess_node()`.

## Theme Hook Suggestions    

The default implementation of template files and theme functions offer a very generic set of markup that is sufficient, but not ideal in all cases. When doing a standard override, such as copying `block.tpl.php` into a theme, the changes made will apply site-wide whenever a block is rendered. At times this the desired result, but you'll often want to make changes to a specific block, a set of blocks provided by a specific module, or even a group of blocks in a specific region.

Theme hook suggestions allow you to implement targeted overrides in your theme for both template files and theme functions with naming patterns. The options and naming patterns vary depending on what type of object you are working with. During the preprocess stage, before each template is rendered, a variable called `$theme_hook_suggestions` is created and populated with alternative hook suggestions.

## Suggestions and Template Files    

All of the common template files listed in Table 15–1 can be overridden to allow for more targeted customization by simply changing the name of the template file. When working with blocks, for example, Drupal suggests the options in Listing 15–15 during `template_preprocess_block()`.

**Listing 15–15**. Excerpt from `template_preprocess_block()` where template suggestions for block template files are defined.

```html
<?php
$variables['theme_hook_suggestions'][] = 'block__' . $variables['block']->region;
$variables['theme_hook_suggestions'][] = 'block__' . $variables['block']->module;
$variables['theme_hook_suggestions'][] = 'block__' . $variables['block']->module . '__' . $variables['block']->delta;
```

Drupal automatically converts the underscores to dashes and searches for these templates in your theme when determining which one to use. This code translates to the suggestions shown in Table 15–4.

**Table 15–4**. Template suggestions for blocks.

Suggestion | Template File Equivalent | Description
:--------- | :----------------------- | :----------
`block` | `block.tpl.php` | Default block implementation.
`block__REGION` | `block--REGION.tpl.php` | REGION is replaced with the theme region name, and the template targets blocks in that region.
`block__MODULE` | `block--MODULE.tpl.php` | MODULE is replaced with the name of the module that created the block. For example, a template file that targets custom blocks would be `block--block.tpl.php` and a block created by the menu module would be targeted by using `block--menu.tpl.php`.
`block__MODULE__DELTA` | `block--MODULE--DELTA.tpl.php` | The DELTA value, which used to be a number in previous versions, is the system name of the block as defined by the module. For example, to target the System module's Navigation block, you would use `block--system--navigation.tpl.php`. In this example, "system" is the module and "navigation" is the delta.

### Page-Level Suggestions

Because of their special nature as the highest-level template files in Drupal, both `html.tpl.php` and `page.tpl.php` are given special attention when it comes to generating their suggestions. A function called `theme_get_suggestions()` is used to automatically generate suggestions using arguments based on the context of the current page. This means that if you wanted to, you could literally have a different version of these template files for every page on your site. Of course, this is something you should never even think about doing, but in certain cases, like a very different home page or landing page, having a different `page.tpl.php` makes perfect sense.

As mentioned, the theme hook suggestions for these files are generated with the help of arguments. Arguments in Drupal are the elements or pieces of system path of a page. For example, when viewing the URL `http://yoursite.com/node/1`, the first argument is "node" and the second argument is "1". Understanding arguments in Drupal is one of the key things that will help you understand Drupal. They are extremely useful in determining context and can allow you to perform more advanced manipulations in your theme.

Figure 15–17 illustrates how you can use theme hook suggestions and arguments to make separate `page.tpl.php` and `html.tpl.php` templates for just about any page on your site.

![Diagram of how theme hook suggestions work](http://themery.com/sites/default/files/figure-15-17.png)

**Figure 15–17**. Suggestions for `page.tpl.php` on different types of pages.

<small>* Drupal's front page is set to "node" by default under Admin » Configuration » Site Information. This page is not a typical node. It is a custom page provided by the node module's <code>node_page_default()</code> function. It lists posts that have been marked as "Promote to front page." The "front" suggestion is specific to the front page (or home page), regardless of what type of page it is. Should you change your front page to a different path, additional suggestions will become available to you.</small>

<blockquote><b>Caution</b> Figure 15–17 lists examples of named paths that you'll likely encounter when using contributed modules such as views and panels. These become system paths and can be used as template suggestions. However, attempting to create a template file using a path that was created using a custom alias (or the Pathauto module) such as <code>about/team</code> for a <code>node/1</code> will not work. The same applies to taxonomy, terms, and user profiles. The real system path is always required when working with templates.</blockquote>

Some observations of `$theme_hook_suggestions` include:

- Underscores are used instead of dashes.
- File extensions are not present because these hooks can be implemented as theme functions or template files. At this stage in the process, it doesn't matter whether a template or a theme function will be used. When it's time to render the content, `theme()` will determine which should be used and make the necessary adjustments.
- Each suggestion begins with a `hook__` (double-underscore) prefix. In the example shown in Listing 15–15, that hook is block. This allows Drupal to fall back on the generic theme hook, which in this case is block, and use `block.tpl.php` when a more specific template, like `block--module.tpl.php`, doesn't exist.

The order in which these suggestions appear in the `$theme_hook_suggestions` variable determines which hook/template file will be used in FILO (first in, last out) order. When it comes time to render the template, the last suggestion will be used, with one exception. A variable called `$theme_hook_suggestion` (note that it is singular, not plural) is also available. If it's set by a module or theme, it will take precedence over anything defined in `$theme_hook_suggestions`.

<blockquote><b>Tip:</b> Use the <code>dpm()</code> function (provided with the Devel module) inside the generic template file you are working with to find out what options are available. <code>&lt;?php dpm($theme_hook_suggestions); ?></code> will show the options that are available for the page you are working on.</blockquote>

## Suggestions and Theme Functions    

As explained in the "Suggestions and Template Files" section, alternate `$theme_hook_suggestions` are usually defined in the preprocess function for that hook. This works well because template files usually serve a specific purpose, like printing a specific entity such as a node or block. Theme functions, however, are much more diverse and end up being used within many different types of output, such as form elements, fields, and render elements. Module developers may also use theme functions to create one-off, custom content. This makes the prospect of implementing a theme function override of a widely used function such as `theme_links()` much less attractive, as it could potentially break styling in unexpected places all over your site.

Luckily, theme hook suggestions also exist for many theme functions, and Drupal core has implemented suggestions for some of the popular theme functions, like `theme_links()`. Using theme hook suggestions with theme functions simply means that you can choose to override a theme function in a specific context as opposed to overriding the base theme function, which would have a site-wide effect.

As mentioned, `theme_links()` is a good example of where to use theme hook suggestions when overriding theme functions. This theme function is used in many, many places, such as the main navigation, node, comment, and contextual links. Note that to implement the functions named in the "Theme function equivalent" column in Table 15–5, you need to replace THEME with the name of your theme in `template.php`.

**Table 15–5**. Some example template suggestions for `theme_links()`.

Suggestion | Theme Function Equivalent | Description
:--------- | :------------------------ | :----------
`links` | `THEME_links()` | Default implementation, which is used for all implementations unless a more specific implementation like those below is specified.
`links__node` | `THEME_links__node()` | Targeted implementation of `theme_links()` that only applies to links lists inside of nodes.
`links__comment` | `THEME_links__comment()` | Targeted implementation of `theme_links()` that only applies to links lists inside of comments.
`links__contextual` | `THEME_links__contextual()` | Targeted implementation of `theme_links()` that only applies to links generated for contextual links.
`links__contextual__node` | `THEME_links__contextual__node()` | Targeted implementation of `theme_links()` that only applies to contextual links inside of nodes.

You'll notice in Drupal's default `page.tpl.php` file, located in the main and secondary menus are printed using suggestions. You might also notice that theme functions called `theme_links__system_main_menu()` and `theme_links__system_secondary_menu()` do not exist, and that's okay. In this case, the base hook, or the fallback, `theme_links()` will be used unless a more targeted theme function is created (see Listing 15–16).

**Listing 15–16**. Excerpt from `modules/system/page.tpl.php`.

```html
<?php if ($main_menu || $secondary_menu): ?>
  <div id="navigation">
    <div class="section">
      <?php print theme('links__system_main_menu', array('links' => $main_menu, 'attributes' => array('id' => 'main-menu', 'class' => array('links', 'inline', 'clearfix')), 'heading' => t('Main menu'))); ?>
      <?php print theme('links__system_secondary_menu', array('links' => $secondary_menu, 'attributes' => array('id' => 'secondary-menu', 'class' => array('links', 'inline', 'clearfix')), 'heading' => t('Secondary menu'))); ?>
    </div>
  </div> <!-- /.section, /#navigation -->
<?php endif; ?>
```

In this situation, the theme hook suggestions are hard-coded into the function arguments. When `theme()` processes this, it will check to see if an implementation of `theme_links__system_main_menu()` exists first. If the function is found, it will be used to render the content. If not, the original (or fallback) `theme_links()` will be used instead. `theme()` handles this automatically and can determine the base hook from the use of the double underscore that appears directly after it.

<blockquote><b>Caution:</b> It's important to note that theme hook suggestions are NOT the same as theme hooks. Given what you've learned about theme hook suggestions, it's natural to think that preprocess and process functions can be written for the specific suggestion. Theme hooks, which are the default implementation and suggestion, are specifically registered in an implementation of <code>hook_theme()</code>. This means that you may create a preprocess function called <code>THEME_preprocess_page()</code> but you may not use <code>THEME_preprocess_page__front()</code>.</blockquote>
