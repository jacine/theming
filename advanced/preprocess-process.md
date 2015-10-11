# Preprocess and Process Functions

Preprocess functions are a theme developer's best friend. There are so many use cases where preprocess functions can make your life easier, your code more efficient, and your template files clean and crisp. If you haven't used them before, either because you think you don't need them or are afraid of delving too deep into PHP, you are truly missing out. We hope to change that.

By now you are familiar with the general purpose of template files, which is mainly to provide markup and print variables. But what if you'd like to change those variables or add your own? Your first inclination might be to create a template file and do everything there, but that is often the wrong way to go.

Preprocess functions were designed for this exact purpose. When implementing a preprocess or process function you are basically telling Drupal, "Hey, wait! I have some changes to make to this data before you send it off for rendering." It's sort of like an editor getting a final review of an article before it's allowed to be published. By definition, "preprocess" is a phase of processing that happens before templates are rendered. "Process" functions, which are new in Drupal 7, serve the same purpose, with the only difference being that they run later (after preprocess) in the processing cycle.

A good example of how Drupal uses preprocess and process functions is the `$classes_array` and `$classes` variables. In `template_preprocess()` in Listing 16–1, which is the default implementation of preprocess by Drupal and the first preprocess function called, the `$classes_array` variable is initialized; see http://api.drupal.org/api/function/template_preprocess/7.

**Listing 16–1**. Excerpt from `template_preprocess()` where `$classes_array` is defined.

```html
<?php
function template_preprocess(&$variables, $hook) {
  // Initialize html class attribute for the current hook.
  $variables['classes_array'] = array(drupal_html_class($hook));
}
```

This first step provides a class indicating the hook that's being used. For example, if this preprocess function is being called for a node, this code will add the class node to this array. After this function runs, all modules and themes also have a chance to run it themselves and add or change any of the variables. Next up is the Node module, which implements `template_preprocess_node()`; see http://api.drupal.org/api/function/template_preprocess_node/7. As you can see in Listing 16–2, quite a few classes are added to this array.

**Listing 16–2**. Excerpt from `template_preprocess_node()` where additional classes are added to the `$classes_array` variable.

```html
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

```html
<?php
function template_process(&$variables, $hook) {
  // Flatten out classes.
  $variables['classes'] = implode(' ', $variables['classes_array']);
}
```

Listings 16–1 through 16–3 illustrate some of the flexibility and power that Drupal provides with preprocess and process functions as well as the order in which these functions occur. The most important thing to understand is that in the theme layer, you've got the last call on all of these variables. You can easily add, modify, and remove any variables you please by simply implementing preprocess and process functions in your theme; this will be covered in more detail in the following pages.

The big advantage of using preprocess functions is that they allow you to keep most of the logic outside of your template files. This allows for cleaner and easier to understand template files plus more efficient themes that are easier to maintain, manage, and extend over time. There are many changes you can make, such as affecting classes and modifying existing variables, that don't require any changes to template files at all — just a few simple lines of code.
