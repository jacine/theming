# Using the Render API

## What is a Render Array?

Many of the variables in template files are straightforward, but you'll notice that some of the variables are printed along with a function called `render()`. Render arrays are structured arrays that contain nested data and other information needed by Drupal to turn them into HTML using Drupal's Render API. Variables that are render arrays are generally easy to spot in template files because they are printed using a function called `render()`.

In `page.tpl.php`, you'll notice that all of the regions are printed using the `render()` function. Each region is an element (another array) nested inside the `$page` array. The code in Listing 16–13 is all that's needed to render each region. Each `render()` call returns fully formatted HTML for all the contents of the render array.

**Listing 16–13**. Printing regions in `page.tpl.php` using the `render()` function.

```php
<?php print render($page['sidebar_first']); ?>
```

In prior versions of Drupal, you would just include `<?php print $sidebar_first; ?>`, which contained a fully formatted HTML string ready for printing. This worked, of course, but it wasn't very flexible. Let's face it; there are only so many things you can do with a big glob of HTML markup at that stage.

In Drupal 7, these variables are sent to templates as nicely structured arrays. Instead of a glob of HTML markup, you get an array containing all sorts of information about the content inside it, down to attributes of specific links deep inside of it. This makes it incredibly easy to target specific content of the arrays and make any sort of changes you want to it at the last possible minute before it's rendered in the first place.

To find out what's inside this array, use the `dpm()` function provided with the Devel module to print it inside of `page.tpl.php`: `<?php dpm($page['sidebar_first']); ?>`. As you can see in Figure 16–4, there are two top-level render elements inside this array, the Search form block and the Navigation block, which are currently printing in the first sidebar.

![Screenshot of sidebar_first region variables array using dpm($page['sidebar_first']](http://themery.com/sites/default/files/figure-16-4.png)

**Figure 16–4**. Contents of the `$page['sidebar_first']` render array printed from `page.tpl.php` using `dpm()`.

## Identifying Render Elements

An easy way to identify arrays as render elements is the presence of properties. Render elements are always arrays, and they always contain properties that always begin with a hash tag. In Figure 16–4, you can immediately tell that `$page['sidebar_first']` is a render element because it contains a few properties: `#sorted`, `#theme_wrappers`, and `#region`. These properties are used by `drupal_render()` which is called when using `drupal_render()` to determine how to render the output. For details about `drupal_render()` see http://api.drupal.org/api/function/render/7.

As themers, you won't be getting deep into the more developer-centric properties, but there are a few that will be helpful for you to make sense of out what these arrays mean. These are described in Table 16–1.

**Table 16–1**. Helpful render element properties. See the documentation at http://api.drupal.org/api/function/drupal_render/7 for more information.

Property | Description
:------- | :----------
`#theme` | Specifies the theme hook, which can be either a function or a template to use when rendering the element.
`#theme_wrappers` | An array containing theme hook(s) to be used to wrap the rendered children of the element. For example, when theming a block, the `#theme` property would be block and the `#theme_wrappers` property would contain region. This ensures that after the block(s) are rendered, the children would be run through the region template as well.
`#type` | The type of element that will be rendered. The default properties for element types are defined in `hook_element_info()` implementations.
`#prefix` and `#suffix` | A string containing markup to be placed before (prefix) or after (suffix) the rendered element.
`#weight` | A number that is used to sort the elements to determine the order in which they will print.
`#sorted` | A Boolean (TRUE or FALSE) that indicates whether or not the children have been sorted. For example, this is used in conjunction with the `#weight` property to sort the blocks in a region. When reordering blocks in a theme via `hook_page_alter()`, you'll need to specify `#sorted` => FALSE in addition to the #weight to trigger a new sort when you need to move a block to any other position than below the already sorted elements.
`#attached` | The `#attached` property is used to specify corresponding CSS, JavaScript, or libraries to load when the element is rendered.

## Manipulating the Output of Render Elements

As mentioned, having a structured array to work with is far more flexible than a bunch of HTML. This allows you to make only the changes you want to make with ease, whether big or small, without having to re-write code from scratch.

The prospect of using render arrays to generate markup and using alter hooks in general are completely new concepts to Drupal theme developers. It's very different than what you are used to, in a good way, but it takes some getting used to. In a lot of ways it's easier than creating templates and theme functions for one-off implementations. The biggest issues front-end developers face when using the Render API are:

1. Thinking about generating markup differently.
2. Figuring out how to modify the content of a render array.
3. Getting comfortable with implementing alter hooks.

Unlike theme hooks, render arrays are modified using alter hooks, not preprocess functions and templates. This can be confusing at first because render arrays are similar to theme hooks in that their purpose is to ultimately generate HTML markup, and they use templates and theme functions to do so. With render arrays, the #theme property, which allows you to define which theme function or template should be used to render the element, is just one of many properties used and can be changed at any time. In general, you'll use templates and theme functions to modify the markup itself, and you'll use alter hooks to modify contents, structure, or placement of the elements before it's rendered.

The following sections contain a few examples of things you can do with render arrays.

### Generate New Content on the Fly

Generating new content is as simple as adding a new element to the page array. Listing 16–14 shows the addition of a new element called `new_stuff` to the preexisting Highlighted region by implementing `hook_page_alter()` in a theme's `template.php`.

**Listing 16–14**. Adding a new element to the highlighted region

```php
<?php
/**
 * Implements hook_page_alter().
 */
function mytheme_page_alter(&$page) {
  $page['highlighted']['new_stuff'] = array(
    '#type' => 'container',
    '#attributes' => array('class' => 'my-container'),
  );
  $page['highlighted']['new_stuff']['heading'] = array(
    '#type' => 'html_tag',
    '#tag' => 'h2',
    '#value' => t('Heading'),
    '#attributes' => array('id' => 'my-heading'),
  );
  $page['highlighted']['new_stuff']['list'] = array(
    '#theme' => 'item_list',
    '#items' => array(
      'First item',
      'Second item',
      'Third item',
    ), 
  );
}
```

The first thing you did was name your new element "new_stuff", gave it a `#type` of container, and defined a class attribute of `my-container`. Note that container is an element, defined in `system_element_info()`, which uses the `theme_container()` theme function as a theme wrapper by default. This means the children of your element (heading and list) will be run through `theme_container()`. The resulting markup is shown in Listing 16–15.

**Listing 16–15**. The output generated for `$page['highlighted']['new_stuff']` by `theme_container()`.

```html
<div class="my-container">...</div>
```

Then you added a subelement called “heading” and specified the `#type` element property as `html_tag`. This will cause the element to use `theme_html_tag()` when rendering. You also specified `#tag`, `#value`, and `#attributes` properties. These are parameters of the `theme_html_tag()` function as you can see at http://api.drupal.org/api/function/theme_html_tag/7. The resulting markup is shown in Listing 16–16.

**Listing 16–16**. The output generated for `$page['highlighted']['new_stuff']['heading']` by `theme_html_tag()`.

```html
<h2 id="my-heading">Heading</h2>
```

Finally, you added a subelement called “list”. Here you specified `item_list` as the `#theme` property and included an array containing your #items, which is a required parameter for `theme_item_list()`. The resulting markup is shown in Listing 16–17.

**Listing 16–17**. The output generated for `$page['highlighted']['new_stuff']['list']` by `theme_item_list()`.

```html
<div class="item-list">
  <ul>
    <li class="first">First item</li>
    <li>Second item</li>
    <li class="last">Third item</li>
  </ul>
</div>
```

When the Highlighted region is rendered, the code in Listing 16–14 produces the final result shown in Listing 16–18.

**Listing 16–18**. The final rendered result of Listing 16-14.

```html
<div class="my-container">
  <h2 id="my-heading">Heading</h2>
  <div class="item-list">
    <ul>
      <li class="first">First item</li>
      <li>Second item</li>
      <li class="last">Third item</li>
    </ul>
  </div>
</div>
```

Caution The previous examples are meant to illustrate how the Render API works to generate content. However, it's worth noting that it should not be abused to output every piece of HTML on a page as separate elements because there can be serious performance implications. Using the markup #type is preferred for small bits of markup, such as headings, instead of `html_tag`, as it requires the `theme_html_tag()` theme function to determine the output.

### Move Content from One Region to Another

Inside a `hook_page_alter()` implementation, you can move the content of regions around at will. Listing 16–19 contains a few simple lines of code that move the contents of the entire first sidebar to the second sidebar, which results in the layout changing from a left sidebar layout to a right sidebar layout on full node pages. In Listing 16–19, you've also moved the breadcrumbs to the bottom of the footer region.

**Listing 16–19**. Relocate the `sidebar_first` region to `sidebar_second` and add breadcrumbs to a new element in the footer region.

```php
<?php
/**
 * Implements hook_page_alter().
 */
function dgd7_page_alter(&$page) {
  // Check that you are viewing a full page node.
  if (node_is_page(menu_get_object())) {
    // Assign the contents of sidebar_first to sidebar_second. $page['sidebar_second'] = $page['sidebar_first'];
    // Unset sidebar_first.
    unset($page['sidebar_first']);
  }
 
  // Add the breadcrumbs to the bottom of the footer region.
  $page['footer']['breadcrumbs'] = array(
    '#type' => 'container',
    '#attributes' => array('class' => array('breadcrumb-wrapper', 'clearfix')), '#weight' => 10,
  );
  $page['footer']['breadcrumbs']['breadcrumb'] = array(
    '#theme' => 'breadcrumb',
    '#breadcrumb' => drupal_get_breadcrumb(),
  );
 
  // Trigger the contents of the region to be re-sorted.
  $page['footer']['#sorted'] = FALSE;
}
```

### Altering Content Inside a Render Array

Altering the contents of a render array to change bits and pieces of the actual content is where you get into a very gray area. It could be argued that a change like this belongs inside a module. When making changes like this, it's important to ask yourself whether or not the changes you are making should still apply when the theme you are developing is not active. Listing 16–20 changes the "View" and "Edit" tabs to read "Profile" and "Edit profile" on user profile pages.

**Listing 16–20**. Implements `hook_menu_local_tasks_alter()` to change tab names on user profile pages.

```php
<?php
/**
 * Implements hook_menu_local_tasks_alter().
 */
function dgd7_menu_local_tasks_alter(&$data, $router_item, $root_path) {
  if ($root_path == 'user/%') {
    // Change the first tab title from 'View' to 'Profile'.
    if ($data['tabs'][0]['output'][0]['#link']['title'] == t('View')) {
        $data['tabs'][0]['output'][0]['#link']['title'] = t('Profile');
    }
    // Change the second tab title from 'Edit' to 'Edit profile'.
    if ($data['tabs'][0]['output'][1]['#link']['title'] == t('Edit')) {
      $data['tabs'][0]['output'][1]['#link']['title'] = t('Edit profile');
    }
  }
 }
```

## Notable Render Arrays in Core Templates

There are quite a few render array variables scattered across core templates that are worth noting. `hook_page_alter()` contains the entire page so it can always be used to alter anything. However, finding that particular anything is not always trivial as other modules can move stuff around, so using more specific alters is advised. Table 16–2 is a quick reference of notable render arrays. This is by no means a full list, but it covers quite a bit and should give you an idea of how to begin figuring out where to look to edit these things.

**Table 16–2**. Notable render arrays in core templates.

Variable | Found In | Alter Hook | Description
:------- | :------- | :--------- | :----------
`$page` | `page.tpl.php` | `hook_page_alter()` | Contains the entire page from regions down to fields and comments.
`$content` | `node.tpl.php`, `comment.tpl.php`, `taxonomy-term.tpl.php`, `hook_node_view_alter()`, `hook_comment_view_alter()`, `hook_taxonomy_term_view_alter()` | Contains the contents of each entity. For more details see http://api.drupal.org/hook_entity_view_alter.
`$tabs` | `page.tpl.php` | `hook_menu_local_tasks_alter()` | Contains primary and secondary tabs, themable via `theme_menu_local_tasks()` and `theme_menu_local_task()`.
`$action_links` | `page.tpl.php` | `hook_menu_local_tasks_alter()` | Contains action links, themable via `theme_menu_local_actions()`.
`$item` | `field.tpl.php` | `hook_field_display_alter()` and `hook_field_display_ENTITY_TYPE_alter()` | Contains display settings for fields, which can adjust label settings or control the formatter used to display the contents of `field.tpl.php`.

## Introducing render(), hide(), and show()

One of the best new theming features of Drupal 7 is the ability to selectively render bits of content in templates. As detailed in the previous sections, the content of some variables (render arrays) is sent to templates as structured arrays instead of chunks of HTML. This is really awesome news for the theme layer.

To understand just how awesome this is, you need to look into the past. In prior versions of Drupal, theming complex nodes with fields wasn't the easiest task. Fields were lumped into the $content variable, and while they could be printed and manipulated individually, there were issues. You had to be very careful to properly sanitize variables, and once you decided to break up the content variable, you needed to rebuild it entirely. This was not future-proof, as the addition of new fields would often require going back to the template file and printing the new field.

In Drupal 7, those problems have been solved quite gracefully. You now have the ability to very easily render individual pieces of content, such as fields, with three new functions called `render()`, `hide()`, and `show()`. They can be used inside theme functions and templates files as well as preprocess and process functions. All three of these functions take a single argument, which is the element (or child) you wish to target.

### hide()

Hides a render element or part of a render element by tricking `drupal_render()` into thinking it has already been printed. Example usage:

```php
<?php hide($element['something']); ?>
```

### show()

Does the opposite of `hide()`. It can be useful to revert a previously applied `hide()` status. Example usage:

```php
<?php show($element['something']); ?>
```

### render()

Converts a render array to HTML markup. It returns HTML, so it should be used along with print in templates. Example usage:

```php
<?php print render($element); ?>
```

To demonstrate these functions in action, look at `node.tpl.php` (see Listing 16–21).

**Listing 16–21**. Excerpt from the default `node.tpl.php` template.

```html
<div id="node-<?php print $node->nid; ?>" class="<?php print $classes; ?> clearfix"<?php print $attributes; ?>>
  <?php print $user_picture; ?>
  <?php print render($title_prefix); ?>
  <?php if (!$page): ?>
    <h2<?php print $title_attributes; ?>><a href="<?php print $node_url; ?>"><?php print $title; ?></a></h2>
  <?php endif; ?>
  <?php print render($title_suffix); ?>
  <?php if ($display_submitted): ?>
    <div class="submitted">
      <?php print $submitted; ?>
    </div>
  <?php endif; ?>
  <div class="content"<?php print $content_attributes; ?>>
    <?php
      // Hide the comments and links now so they can be rendered later.
      hide($content['comments']);
      hide($content['links']);
      print render($content);
    ?>
  </div>
  <?php print render($content['links']); ?>
  <?php print render($content['comments']); ?>
</div>
```

As you can see in Listing 16–21, this template is already making use of both `render()` and `hide()` functions out of the box. There are three render arrays in this node template: `$title_prefix`, `$title_suffix`, and `$content`. Inside the `<div class="content">` wrapper, both `$content['links']` and `$content['comments']` are hidden using `hide()`, and then `$content` is rendered directly underneath.

The reason that the comments and links are hidden is to break them out of the $content variable and allow them to be placed outside of the `<div class="content">` wrapper. Both of the items are then rendered afterward using `render()` individually.

Of course, the fun doesn't have to stop at top-level variables. These functions work as deep into the array as you can go. As long as you pass in a proper render element (see the "Render API" section), you'll be able to manipulate it with these functions.

As an example, say you wanted to hide the "Add new comment" link when viewing a node that has a comment form on the page you're viewing. You can simply check to see if the form exists in your array, and then hide that specific link group (comment). The code in Listing 16–22 demonstrates how to do this.

**Listing 16–22**. Hiding the "Add new comment" link when the comment form is present.

```html
<?php
// Hide the "Add new comment" link when the comment form is present.
if (!empty($vars['content']['comments']['comment_form'])) {
  hide($vars['content']['links']['comment']);
 }
// Print the rendered links afterward.
print render($content['links']);
```

Because the `show()` function resets the print status but does not print anything, it can be helpful to revert a previously applied `hide()`. In most cases, you'll likely just use `render()` because it will allow you to print the element as many times as you need, as shown in Listing 16–23.

**Listing 16–23**. Hiding the "Add new comment" link when the comment form is present, but showing it again if some other condition is met.

```html
<?php
// Hide the "Add new comment" link when the comment form is present.
if (!empty($content['comments']['comment_form'])) {
  hide($content['links']['comment']);
  if ($some_exception) {
    show($content['links']['comment']);
  }
}
// Print the rendered links afterward.
print render($content['links']);
```

<blockquote><b>Tip:</b> For complex templates, this code begins to get very messy in templates files. In those situations, it's best to do these operations in preprocess or process functions in order to keep your templates clean and more manageable.</blockquote>
