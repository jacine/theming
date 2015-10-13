# Managing CSS Files

Every good Drupal theme needs a style sheet or two, or ten! You might be caught off guard by the sheer number of CSS files that Drupal loads, before you even start on your theme. Being the modular framework that it is, Drupal uses that same approach for CSS style sheets and JavaScript files. CSS and JavaScript files are provided separately by module — and sometimes a few per module. This is done on purpose for the following reasons:

- It's easier to read and understand the purpose of the code and what module it belongs to.
- It allows Drupal to load only the code needed on a given page.
- It's easier for Drupal to maintain these files and their contents.

That said, in Drupal's theme layer you have full control over all style sheets and scripts. You can do whatever you want with them, literally. If you decide you don't want to load any style sheets from modules, you can remove them all. If you aren't happy with a few files, you can override them individually by removing them or override them and change the contents within the theme. You can even change the order in which the files load if you want to. This section will show you how to do all of that.

## Aggregation and Compression

As mentioned, Drupal has many style sheets. Of course, you want to keep the number of files at a minimum on your live sites for performance reasons, so Drupal has a way of handling this. During development, it's normal to deal with anywhere from 10-40 CSS files, and even more if you are working on sites in languages that display text in right-to-left order. In the Performance section at `admin/config/development/performance` there are options to aggregate and compress CSS and JavaScript files. When turned on, Drupal will minify and combine the files into as few automatically generated files as possible. This also effectively works around the Internet Explorer 31 style sheet limit bug.

Drupal aggregates files in two ways: it creates a per-site aggregation file from files that would be loaded on every page, and it creates a per-page aggregation files for the remaining files that are conditionally loaded depending on the page. For CSS files, it further aggregates by media type. To remain correct, if the contents of CSS and JavaScript files are changed, when the site cache is cleared Drupal will regenerate the aggregated versions of the files and give them a different name. Enabling aggregation and compression for CSS files on all live sites is highly recommended, as it will speed up page loads quite a bit. This process is very effective and allows themers and developers to continue developing sites in a modular manner, without having to worry about the number of CSS files.

<blockquote><b>Caution</b> Do NOT use the <code>@import</code> directive to load CSS files manually within Drupal. Doing so will cause performance and possible aggregation issues when combined with <code>&lt;link&gt;</code>'ed style sheets and will cause the files to be excluded from override features.</blockquote>

## Patterns and Naming Conventions

In your theme, you are free to name your CSS files whatever you want. Many themes tend to create a directory called "css" in which they place a few style sheets. It's very common to create a `layout.css` for page layout styles and `style.css` for the rest. Some themes, like Zen, take it much further with almost 30 style sheets. How you decide to organize your CSS is completely up to you. There are no restrictions on how many style sheets a theme can have. Most front-end developers have their own way of working, and Drupal is happy to oblige.

### Core and Module CSS Files

Most modules that provide CSS will typically include a file in the root of the module directory called `module-name.css`. Some of the modules have a few CSS files, and the better modules create a separate file for any CSS used to style the administrative user interface. Modules are not restricted to any number or specific organization of CSS files, but developers are generally urged to be conservative and style elements as little as possible.

It's also worth mentioning that Drupal's System module, located in modules/system, contains quite a few CSS files that seem to all be dumped there because there is no better place to put them. Table 16–3 provides a quick description of each, so you have an idea of what their purpose is and can decide whether or not to keep them in your theme.

**Table 16–3**. System module's CSS files, excluding RTL versions.

CSS File | Purpose | Loads on…
:------- | :------ | :--------
`system.base.css` | Contains CSS that is heavily relied upon by JavaScript for certain functionality, including collapsible fieldsets, autocomplete fields, resizable textareas, and progress bars. | Every page.
`system.theme.css` | Contains general styles for many generic HTML and Drupal elements. | Every page.
`system.menus.css` | Contains default styling for menu tree lists, tabs, and node links. | Every page.
`system.messages.css` | Contains default styling for error, warning, and status messages. | Every page.
`system.admin.css` | Contains styles needed on administrative pages throughout Drupal. | Admin pages.
`system.maintenance.css` | Contains styles for installation, maintenance, and update tasks. | Maintenance pages.

### Bi-Directional Text Support

One of the things Drupal is known for is its superb language support. This includes bi-directional text support. While most languages display text from left-to-right (LTR) on screen, certain languages, such as Arabic and Hebrew, display text from right-to-left (RTL) on screen. Browsers handle much of the styling differences needed by reading the `dir` attribute defined in the `<html>` tag and using User Agent CSS files, but many times CSS floats, text alignment, and padding need to be accounted for in CSS, especially when you are running a site with multiple languages.

Drupal handles RTL style sheets in an automated way based on CSS file naming conventions. If you have a style sheet named style.css, which contains the CSS for the LTR version of the site, you can simply create another file called style-rtl.css to contain the necessary tweaks to fix the display for the RTL version. Drupal will automatically load it when needed, directly after the original file so that the same selectors can be used and RTL styles will override the LTR styles, taking advantage of the natural CSS cascade. When writing CSS for a site that will support both LTR and RTL displays, it is customary to write the CSS for the LTR version first, while keeping track of what will need to change (per property) with a comment. This is one of the coding standards Drupal has adopted for core and contributed CSS files. Listing 16–36 shows an example.

**Listing 16–36**. Example CSS denoting a LTR property and the RTL version.

```css
/* In style.css:
 .my-selector floats content to the left, which is LTR-specific, so an inline comment is added to note this.
*/
.my-selector {
  border: solid 1px #ccc;
  float: left; /* LTR */
}
 
/* In style-rtl.css:
The RTL version of .my-selector needs to be overriden and floated right instead of left. */
.my-selector {
  float: right;
}
```

## Adding, Removing, and Replacing CSS Files

There are three ways to manipulate CSS files within Drupal themes. This section will explain what the implementation options are, the reasons for each method, and when it's advantageous to use certain methods over others.

### Quick and Dirty Style sheets via .info Files

Adding style sheets via your theme's `.info` file is the easiest way to add a CSS file to your theme; see Listing 16–37 and Listing 16–38. However, there are a some drawbacks to doing this in certain situations:

1. Any style sheet you define in the `.info` file will load on every page.
2. You don't have the full use of the features available in `drupal_add_css()`. For example, you can't add conditional style sheets for Internet Explorer or change the weight of a module's CSS file in your `.info` file.

**Listing 16–37**. `.info` syntax for adding style sheets.

```ini
stylesheet[CSS media type][] = path/to/file.css
```

**Listing 16–38**. Typical `.info` style sheet definition example.

```ini
stylesheets[all][] = css/layout.css
stylesheets[all][] = css/style.css
stylesheets[print][] = css/print.css
```

Caution Style sheets may also be removed via `.info` files by creating an entry for a file as if you were overriding it, but then not actually including the file in the theme directory. However, there is a bug that allows these style sheets to return when AJAX rendering occurs. To be safe it's best to remove style sheets in `hook_css_alter()`; this is explained later in this section.

### Conditionally Loading Style sheets with drupal_add_css()

`drupal_add_css()` is the main function used by modules and themes to add CSS files via PHP code. Some themes use it in their `template.php` file, typically within preprocess functions. One of the advantages of using `drupal_add_css()` in the theme layer as opposed to defining CSS files in a `.info` file is that files can be conditionally loaded based on certain criteria or context. For example, you may want to create a special CSS file that only loads on your site's home page. In your theme's `template.php`, you could do this within `template_preprocess_html()`, as shown in Listing 16–39.

**Listing 16–39**. Adding a style sheet that loads only on the home page.

```php
<?php
function mytheme_preprocess_html(&$variables) {
  // Add a stylesheet that prints only on the homepage.
  if ($variables['is_front']) {
    drupal_add_css(path_to_theme() . '/css/homepage.css', array('weight' => CSS_THEME));
  }
}
```

There are many different options for adding CSS to your pages in Drupal using `drupal_add_css()`, some of which include:

- Specifying the type as "inline" to print a block of CSS code within `<head>`, as opposed to adding a CSS file.
- Specifying the "group" of a file to determine where the file should appear using constants such as `CSS_SYSTEM` (top), `CSS_DEFAULT` (middle), and `CSS_THEME` (bottom).
- Specifying the "weight" of a file to control the order in which it loads within its group.
- Adding conditional style sheets to serve different files to different browsers.
- Adding externally hosted CSS files.
- Forcing a CSS file to be excluded from the aggregation and compression process.
- Adding Conditional Style sheets for Internet Explorer

According to Wikipedia at the time of this writing, about 43 percent of users are visiting web pages using Internet Explorer. This statistic varies from source to source, but for many of you, supporting older versions of Internet Explorer is a fact of life. Using conditional style sheets is considered a best practice when the need arises to write CSS that targets Internet Explorer.

One of the great new features in Drupal 7 is that conditional style sheets can be added using `drupal_add_css()`. In fact, all three of Drupal's core themes do this in `template_preprocess_html()`. The reason this is done in `template.php` is that `.info` files only have very basic support for `drupal_add_css()`. Listing 16–40 and Listing 16–41 demonstrate how this works using code from the Seven theme as an example.

**Listing 16–40**. Excerpt from the Seven theme, using `drupal_add_css()` to add conditional style sheets for IE in `template_preprocess_html()`.

```php
<?php
function seven_preprocess_html(&$variables) {
  // Add conditional CSS for IE8 and below.
  drupal_add_css(path_to_theme() . '/ie.css', array('group' => CSS_THEME, 'browsers' => array('IE' => 'lte IE 8', '!IE' => FALSE), 'preprocess' => FALSE));
  // Add conditional CSS for IE6.
  drupal_add_css(path_to_theme() . '/ie6.css', array('group' => CSS_THEME, 'browsers' => array('IE' => 'lt IE 7', '!IE' => FALSE), 'preprocess' => FALSE));
}
```

**Listing 16–41**. The source code that results from adding IE conditional style sheets with `drupal_add_css()`.

```html
<!--[if lte IE 8]>
  <link type="text/css" rel="stylesheet" href="http://drupal-7/themes/seven/ie.css?l40z2j" media="all" />
<![endif]-->
<!--[if lt IE 7]>
  <link type="text/css" rel="stylesheet" href="http://drupal-7/themes/seven/ie6.css?l40z2j" media="all" />
<![endif]-->
```

The code in Listing 16–40 and Listing 16–41 gives you two conditional style sheets that will load for Internet Explorer only. The first style sheet will load for Internet Explorer 8 and under, and the second style sheet will load for versions of Internet Explorer prior to IE7.

### Completely Control Style sheets Using hook_css_alter()

Drupal core and modules add CSS files individually via the `drupal_add_css()` function. During `template_process_html()`, a variable called `$styles` is created; it contains the fully formatted HTML output for all the style sheets that are specified for each page. This variable is eventually printed inside the `<head>` tags in the `html.tpl.php` template file, as shown in Listing 16–42.

**Listing 16–42**. `$styles` variable is created in `template_process_html()` for use in `html.tpl.php`.

```php
<?php
/**
 * Implements template_process_html().
 */
function template_process_html(&$variables) {
  ...
  $variables['styles'] = drupal_get_css();
  ...
}
```

During the call to `drupal_get_css()`, Drupal gathers up all the CSS files previously added, and then provides an opportunity for any modules or themes to make changes by calling `drupal_alter('css', $css)`. At this time, Drupal looks for functions in modules and themes that fit the naming pattern `hook_css_alter()`, where the word “hook” in the function name is replaced by the module or theme name implementing it. This function allows for the most granular control over all aspects of your CSS files.

An example of why a module might want to implement `hook_css_alter()` can be found in the Locale module. The Locale module checks to see if the language direction is right-to-left, and if so, finds the related RTL versions of the CSS files and adds them to the page.

In themes, the main reasons to implement `hook_css_alter()` is to remove or override CSS files provided by modules. An example of this can be found at the bottom of Seven theme's `template.php` file (see Listing 16–43). Seven chooses to override the style sheet `vertical-tabs.css` file provided by core with its own version.

**Listing 16–43**. The Seven theme's `hook_css_alter()` implementation.

```php
<?php
/**
 * Implements hook_css_alter().
 */
function seven_css_alter(&$css) {
  // Use Seven's vertical tabs style instead of the default one.
  if (isset($css['misc/vertical-tabs.css'])) {
    $css['misc/vertical-tabs.css']['data'] = drupal_get_path('theme', 'seven') . '/vertical- tabs.css';
  }
  // Use Seven's jQuery UI theme style instead of the default one.
  if (isset($css['misc/ui/jquery.ui.theme.css'])) {
    $css['misc/ui/jquery.ui.theme.css']['data'] = drupal_get_path('theme', 'seven') . '/jquery.ui.theme.css';
  }
}
```

<blockquote><b>Caution</b> Overriding a module's CSS files in <code>.info</code> files (creating an entry with the same CSS file name) will work, but not always in an efficient way. Style sheets that are defined in `.info` files will load on every page. Whether or not they are actually needed is never taken into account. This is not the case when using `hook_css_alter()` as you are given the opportunity to make sure the file is set to load before attempting to replace it.

## Exercise: Managing Style sheets in Your Theme

In this section, you've learned quite a few ways to manipulate CSS files in Drupal's theme layer. Now you'll go through the steps again with practical examples.

### Exercise A: Define style sheets for all pages in the .info file

**Step 1**: Begin by creating a new directory in your theme called `css` in `sites/all/themes/mytheme`. This step is optional but helpful for theme file organization.

**Step 2**: Create two files inside the `css` directory called `style.css` and `print.css`.

**Step 3**: Open the `sites/all/themes/mytheme/mytheme.info` and add the following two lines to define the style sheets so Drupal knows to load them:

```ini
stylesheets[all][] = css/style.css style sheets[print][] = css/print.css
```

**Step 4**: Clear the site cache at `admin/config/development/performance`. Once you return to the front end of your site, you'll see that both files have been added.

### Exercise B: Add a conditional style sheet for IE using drupal_add_css()

**Step 1**: Create a file inside the `css` directory called `ie.css`.

**Step 2**: Create a file in the root of the theme directory called `template.php` if you haven't already done so, and make sure to include `<?php` at the top of the file.

**Step 3**: Use the following code to implement `template_preprocess_html()` and load the IE style sheet using `drupal_add_css()`:

```php
<?php
/**
 * Implements template_preprocess_html().
 */
function mytheme_preprocess_html(&$vars) {
  // Add conditional stylesheet that targets Internet Explorer 8 and below.
  drupal_add_css(path_to_theme() . '/css/ie.css', array('weight' => CSS_THEME, 'browsers' => array('IE' => 'lte IE 8', '!IE' => FALSE), 'preprocess' => FALSE));
}
```

### Exercise C: Add a custom style sheet for the homepage using drupal_add_css()

You'll use the `$is_front` variable, which already exists, to detect if the home page is being displayed and then add the `homepage.css` style sheet. Add this code directly above the conditional style sheet code you added in Exercise B.

```php
<?php
// Add a stylesheet that prints only on the homepage.
if ($variables['is_front']) {
  drupal_add_css(path_to_theme() . '/css/homepage.css', array('weight' => CSS_THEME));
}
```

### Exercise D: Override and remove module CSS files using hook_css_alter()

To implement `hook_css_alter()`, you'll need to create a function called `mytheme_css_alter()` in your `template.php` file. The `$css` parameter, which is passed by reference, contains all the style sheets in array format, and you can do what you please with it. The following code shows how to remove the `node.css` file if it's set to load.

```php
<?php
/**
 * Implements hook_css_alter().
 */
function mythemename_css_alter(&$css) {
  // Remove the node.css file.
  if (isset($css['modules/node.css'])) {
    unset($css['modules/node.css']);
  }
}
```
