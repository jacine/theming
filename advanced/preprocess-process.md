# Preprocess and Process Functions

Preprocess functions are a theme developer's best friend. There are so many use cases where preprocess functions can make your life easier, your code more efficient, and your template files clean and crisp. If you haven't used them before, either because you think you don't need them or are afraid of delving too deep into PHP, you are truly missing out. We hope to change that.

By now you are familiar with the general purpose of template files, which is mainly to provide markup and print variables. But what if you'd like to change those variables or add your own? Your first inclination might be to create a template file and do everything there, but that is often the wrong way to go.

Preprocess functions were designed for this exact purpose. When implementing a preprocess or process function you are basically telling Drupal, "Hey, wait! I have some changes to make to this data before you send it off for rendering." It's sort of like an editor getting a final review of an article before it's allowed to be published. By definition, "preprocess" is a phase of processing that happens before templates are rendered. "Process" functions, which are new in Drupal 7, serve the same purpose, with the only difference being that they run later (after preprocess) in the processing cycle.

A good example of how Drupal uses preprocess and process functions is the `$classes_array` and `$classes` variables. In `template_preprocess()` in Listing 16–1, which is the default implementation of preprocess by Drupal and the first preprocess function called, the `$classes_array` variable is initialized; see http://api.drupal.org/api/function/template_preprocess/7.

**Listing 16–1**. Excerpt from `template_preprocess()` where `$classes_array` is defined.

```php
<?php
function template_preprocess(&$variables, $hook) {
  // Initialize html class attribute for the current hook.
  $variables['classes_array'] = array(drupal_html_class($hook));
}
```

This first step provides a class indicating the hook that's being used. For example, if this preprocess function is being called for a node, this code will add the class node to this array. After this function runs, all modules and themes also have a chance to run it themselves and add or change any of the variables. Next up is the Node module, which implements `template_preprocess_node()`; see http://api.drupal.org/api/function/template_preprocess_node/7. As you can see in Listing 16–2, quite a few classes are added to this array.

**Listing 16–2**. Excerpt from `template_preprocess_node()` where additional classes are added to the `$classes_array` variable.

```php
<?php
function template_preprocess_node(&$variables) {
  // Gather node classes.
  $variables['classes_array'][] = drupal_html_class('node-' . $node->type);
  if ($variables['promote']) {
    $variables['classes_array'][] = 'node-promoted';
  }
  if ($variables['sticky']) {
    $variables['classes_array'][] = 'node-sticky';
  }
  if (!$variables['status']) {
    $variables['classes_array'][] = 'node-unpublished';
  }
  if ($variables['teaser']) {
    $variables['classes_array'][] = 'node-teaser';
  }
  if (isset($variables['preview'])) {
    $variables['classes_array'][] = 'node-preview';
  }
 }
```

Once again, after `template_preprocess_node()` runs, all modules and themes have a chance to implement their own version, making any changes or additions they want. Once all the preprocess functions have completed, the process functions have their chance. In Drupal core, there are only two process implementations for nodes: `template_process()`, the default implementation, and `rdf_process()`, an implementation by the RDF module.

In `template_process()`, after all the modules and themes have had a chance to modify it, a new variable called `$classes` is created. It contains a string version all of the classes provided in `$classes_array`. The `$classes` variable is printed in the class attribute of the wrapper `<div>` in the `node.tpl.php` template file. This is shown in Listing 16–3.

```php
<?php
function template_process(&$variables, $hook) {
  // Flatten out classes.
  $variables['classes'] = implode(' ', $variables['classes_array']);
}
```

Listings 16–1 through 16–3 illustrate some of the flexibility and power that Drupal provides with preprocess and process functions as well as the order in which these functions occur. The most important thing to understand is that in the theme layer, you've got the last call on all of these variables. You can easily add, modify, and remove any variables you please by simply implementing preprocess and process functions in your theme; this will be covered in more detail in the following pages.

The big advantage of using preprocess functions is that they allow you to keep most of the logic outside of your template files. This allows for cleaner and easier to understand template files plus more efficient themes that are easier to maintain, manage, and extend over time. There are many changes you can make, such as affecting classes and modifying existing variables, that don't require any changes to template files at all — just a few simple lines of code.

## Implementing Preprocess and Process Hooks

Preprocess functions are implemented by creating a function that is named in a certain way. Listing 16–4 shows an example of this naming convention.

**Listing 16–4**. Naming convention for preprocess and process hooks.

```php
<?php
/**
 * Implements template_preprocess_THEMEHOOK().
 */
function HOOK_preprocess_THEMEHOOK(&$variables) {
  // Changes go here.
}
 
/**
 * Implements template_process_THEMEHOOK().
 */
function HOOK_process_THEMEHOOK(&$variables) {
  // Changes go here.
}
```

There are four points to consider in naming these functions:

1. The hook of a default implementation, usually created by a module, is "template". In all other implementations, the hook is replaced by the system name of the module or theme implementing it.
2. Which stage of the process do you want to affect? There are two options: the preprocess, which runs first, or process, which runs after all of the preprocess functions have been executed.
3. The theme hook matches the theme hook as defined in `hook_theme()`, which is ultimately output using either a theme function or a template file.
4. The `&$variables` parameter contains data needed by the theme function or template file rendering it. Since preprocess functions run before templates are rendered, you can make all sorts of changes and additions to its contents.

<blockquote><b>Caution:</b> By default, only theme hooks that have been explicitly defined in <code>hook_theme()</code> are able to use preprocess hooks. For example, <code>hook_preprocess_node()</code> is perfectly fine, but <code>hook_preprocess_node__article()</code> will not work. This is because <code>node__article</code> is a theme hook suggestion, which is a variation of a theme hook but is not actually a real theme hook.</blockquote>

### Default Implementations

Listing 16–5 illustrates what a preprocess implementation for a default theme hook looks like, using `template_preprocess_node()`, which creates variables for the `node.tpl.php` template file as an example. This function resides in `node.module` along with a `hook_theme()` implementation, `node_theme()`, where it defines "node" as a theme hook.

**Listing 16–5**. Naming convention for default implementations of preprocess and process hooks.

```php
<?php
function template_preprocess_node(&$variables) {
  // Changes go here.
  // See http://api.drupal.org/api/function/template_preprocess_node/7 for contents.
}
 
function template_process_node(&$variables) {
  // Changes go here.
  // See http://api.drupal.org/api/function/template_process_node/7 for contents.
}
```

<blockquote><b>Tip:</b> Browsing <a href="http://api.drupal.org">http://api.drupal.org</a> and looking through the default implementations is a great way to learn how the variables were created.</blockquote>

### Theme and Module Implementations

Both modules and themes are able to use preprocess functions in the same way, and a given theme hook can have many preprocess implementations, originating from both modules and themes. This introduces the opportunity for conflicts to occur, so keeping that in mind and knowing the order in which these functions run is important. Preprocess implementations from modules run first, and implementations by themes run last. When dealing with base and subthemes, the base theme will run first and the subtheme will run last. A good way to remember this is that the active theme always wins.

Preprocess functions implemented by Drupal core and modules reside in various files, such as `modulename.module` or `theme.inc`, and many others, while preprocess functions implemented by themes always reside in `template.php`.

As an example, implement a preprocess function for a theme called "dgd7" for the node theme hook. As shown in Listing 16–6, you simply place a function in template.php beginning with the theme name (the implementing hook), followed by `_preprocess_` and the theme hook, which in this case is "node". Finally, you pass in the `&$variables parameter` by reference (the `&;` before the `$` indicates a variable being passed by reference).

**Listing 16–6**. Implementation of `template_preprocess_node()` in a theme.

```php
<?php
/**
 * Implements template_process_node().
 */
function dgd7_preprocess_node(&$variables) {
  // Changes go here.
}
```

The code in Listing 16–6 is all that's needed, along with a quick cache clear, for Drupal to pick up your preprocess function and run it before rendering a node. Now the fun can begin!

## Finding the Contents of $variables

The contents of the `$variables` array are different for each theme hook; even the contents of the same theme hook vary based on other factors, such as the view mode or user role.

The first thing to do after creating the function is to print the array and find out what's inside for you to work with. As explained in the "Finding Available Variables in the Theme Layer" section, using the `dpm()` function is a great way to do this, as shown in Listing 16–7.

**Listing 16–7**. Printing variables to the screen for debugging purposes.

```php
<?php
/**
 * Implements template_preprocess_node().
 */
function dgd7_preprocess_node(&$variables) {
  dpm($variables);
}
```

<blockquote><b>Caution</b> Debugging functions should only be used temporarily during development.</blockquote>

## Preprocess Functions in Action

There are so many things you can change using preprocess functions that we can't possibly get into all of them. Now that you've got your preprocess function all set up and are aware of how to view existing variables, you are equipped with enough knowledge to start making some changes. Let's just jump right in and get started with a few practical examples of how to use preprocess functions.

### Add Classes to Template Wrappers

In the DGD7 theme at http://definitivedrupal.org, the header, sidebar, and footer areas are black and the content area is white. In order to style the contents of each of those sections more easily, you can add a couple of helper classes to the region wrapper. To do this, you'll need to implement a preprocess function for the region theme hook in your template.php file; see Listing 16–8.

**Listing 16–8**. Adding classes to the region wrapper `<div>` using the `$classes_array` variable in `template_preprocess_region()`.

```php
<?php
/**
 * Implements template_preprocess_region().
 */
function dgd7_preprocess_region(&$variables) {
  $region = $variables['region'];
 
  // Sidebars and content area need a good class to style against. You should
  // not be using id's like #main or #main-wrapper to style contents.
  if (in_array($region, array('sidebar_first', 'sidebar_second', 'content'))) {
    $variables['classes_array'][] = 'main';
  }
  // Add a "clearfix" class to certain regions to clear floated elements inside them.
  if (in_array($region, array('footer', 'help', 'highlight'))) {
    $variables['classes_array'][] = 'clearfix';
  }
  // Add an "outer" class to the darker regions.
  if (in_array($region, array('header', 'footer', 'sidebar_first', 'sidebar_second'))) {
    $variables['classes_array'][] = 'outer';
  }
}
```

`$variables['classes_array']` turns into `$class` in the process phase, and the class(es) added during preprocess are automatically modified as a result. So, just like that you've added a class to a region wrapper `<div>`.

The alternative in template files is lengthier. Adding logic to each affected template file would be required, which means you'd need to override the file, even if you didn't need to change the markup. If you have multiple template files for regions, the change would have to be made manually across all of them, which is clearly less efficient as you can see in Listing 16–9.

**Listing 16–9**. Adding classes in preprocess functions can dramatically increase the efficiency of your CSS code.

```css
/* Using classes and ID's provided by default. */
#header fieldset,
#footer fieldset,
.sidebar fieldset {
  border-color: #333;
}
 
/* Using the class added in Listing 16–8, which is more efficient. */ 
.outer fieldset {
  border-color: #333;
}
```

<blockquote><b>Tip:</b> This example changes classes for the region template, but this technique can be applied to any of the major templates, including `html.tpl.php`, `block.tpl.php`, `node.tpl.php` and `comment.tpl.php` in their respective preprocess functions.</blockquote>

### Making Changes to Nodes

Listing 16–10 demonstrates making three changes:

1. Drupal's page title prints in `page.tpl.php`. When a node title prints inside of the `node.tpl.php` file, it's usually because it's being viewed in teaser mode, and therefore, the node title is marked up with an `<h2>` by default. Usually, the content inside the node body also contains one or more `<h2>` tags. Adding a class to single out the node title can make styling easier. Listing 16–10 utilizes the `$title_attributes_array` to add a `node-title` class to help make styling easier.
2. When viewing a node that has a comment form directly under the node links, it doesn't make much sense to have an "Add new comment" link as well. In Listing 16–10, the comment links are hidden when the comment form is below it by using the `hide()` function, which will be covered in more detail later in this chapter.
3. Designs often call for differences when viewing the teaser of a node versus the full page. Listing 16–10 demonstrates using `$variables['teaser']` to suppress the `$submitted` information and truncate the node title to 70 characters when viewing in teaser mode.

**Listing 16–10**. Demonstrates making changes to the display of node content during preprocess.

```php
<?php
/**
 * Implements template_preprocess_node().
 */
function dgd7_preprocess_node(&$variables) {
  // Give the <h2> containing the teaser node title a better class.
  $variables['title_attributes_array']['class'][] = 'node-title';
 
  // Remove the "Add new comment" link when the form is below it.
  if (!empty($variables['content']['comments']['comment_form'])) {
    hide($variables['content']['links']['comment']);
  }
 
  // Make some changes when in teaser mode.
  if ($variables['teaser']) {
    // Don't display author or date information.
    $variables['display_submitted'] = FALSE;
    // Trim the node title and append an ellipsis.
    $variables['title'] = truncate_utf8($variables['title'], 70, TRUE, TRUE);
  } 
}
```

### Add a Change Picture Link Underneath the User Photo

As you've probably noticed by now, there are many variables available to you within the $variables array. These variables can be used to create new variables very easily. You know the path to edit a user profile is `user/UID/edit`, so you can use the information inside of `$variables` to determine whether or not the user viewing the page is the account holder. Once you've determined this, you can easily create a variable containing a link for the user to edit the photo everywhere it appears on the site by implementing `template_preprocess_user_picture()`, as shown in Listing 16–11. Once you do this, you'll be able to print it in the corresponding template, `user-picture.tpl.php`, as shown in Listing 16–12.

**Listing 16–11**. Creating a custom variable for the `user-picture.tpl.php` by implementing `template_preprocess_user_picture()`.

```php
<?php
/**
 * Implements template_preprocess_user_picture().
 * - Add "change picture" link to be placed underneath the user image.
 */
function dgd7_preprocess_user_picture(&$variables) {
  // Create a variable with an empty string to prevent PHP notices when
  // attempting to print the variable.
  $variables['edit_picture'] = '';
 
  // The account object contains the information of the user whose photo is
  // being processed. Compare that to the user id of the user object which    
  // represents the currently logged in user.
  if ($variables['account']->uid == $variables['user']->uid) {
    // Create a variable containing a link to the user profile, with a class
    // "change-user-picture" to style against with CSS.
    $variables['edit_picture'] = l('Change picture', 'user/' . $vars['account']->uid . '/edit',
      array(
        'fragment' => 'edit-picture',
        'attributes' => array('class' => array('change-user-picture')),
      )
    ); 
  }
}
```

**Listing 16–12**. Printing your custom variable into the `user-picture.tpl.php` file, which you've copied into your theme to override.

```html
<?php if ($user_picture): ?>
  <div class="user-picture">
    <?php print $user_picture; ?>
    <?php print $edit_picture; ?>
  </div>
<?php endif; ?>
```
