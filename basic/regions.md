# Working with Regions

Most of the content found on Drupal pages is output inside a region. Typical regions include the header, footer, sidebars, and content (see Figure 15–8); these regions often play a large part in defining the high-level structure of your HTML markup. An option appears in the blocks interface at admin/structure/block for each region, allowing site administrators to control and position the blocks inside them.

**Figure 15-8**. The Bartik theme's regions and block placement options on the Blocks administration page.

![Screenshot of Blocks UI showing placement in regions](http://themery.com/sites/default/files/figure-15-8.png)

Themes have full control over defining and determining the placement of printing and styling regions. An example of what this looks like in the Bartik theme is shown in Figure 15–9.

**Figure 15-9**. Bartik regions filled with custom blocks.

![Screenshot of Bartik regions filled with custom blocks](http://themery.com/sites/default/files/figure-15-9.png)

In addition, themes may also use regions for less obvious purposes in combination with JavaScript or jQuery. Common use cases for regions include containing modal or hidden content to enhance the user interface or embedding blocks into node content.

## Default Regions

Drupal core defines nine regions for themes to utilize programmatically by default. The code in Listing 15–2 duplicates the default core regions in `.info` file format. Like most theme layer implementations, the reason themes define regions is because they want to modify or add to the defaults. Until a theme defines its own regions, Drupal will use the defaults. This means that if the default regions are sufficient for your design, you will not need to define regions in your theme's .info file.

**Listing 15-2**. Drupal's Nine Predefined Theme Regions in Chronological Order.

```ini
regions[page_top] = Page Top
regions[header] = Header
regions[highlighted] = Highlighted
regions[help] = Help
regions[content] = Content
regions[sidebar_first] = Sidebar First
regions[sidebar_second] = Sidebar Second
regions[footer] = Footer
regions[page_bottom] = Page Bottom
```

However, including this code in your theme's `.info` file to begin with is a good practice. Once you define a single region in your theme, it will override core defaults, so having the full list of defaults and commenting out regions that you have disabled (instead of deleting or omitting them entirely) is a good way to keep track of what you're doing with them. You will need some of these regions, namely the `page_top`, `content`, and `page_bottom` regions. These are required and must be printed in every Drupal theme to maintain a properly functioning site. An example of how one might organize regions in an .info file, taking defaults into account, is shown in Listing 15–3.

**Listing 15-3**. An Example of Region Implementation in a Theme's `.info` File.

```ini
; CORE REGIONS - DISABLED;
regions[highlighted] = Highlighted;
regions[help] = Help;
regions[header] = Header;
regions[footer] = Footer

; CORE REGIONS - REQUIRED
regions[page_top] = Page Top
regions[content] = Content
regions[page_bottom] = Page Bottom

; CORE REGIONS
regions[sidebar_first] = Sidebar First
regions[sidebar_second] = Sidebar Second

; CUSTOM REGIONS
regions[my_custom_region] = My Custom Region
```

<blockquote><b>Tip</b> If you're curious where Drupal defines the default regions, take a look at the <code>_system_rebuild_theme_data()</code> function.</blockquote>

As shown in Figure 15–10, the intended display of Drupal's default regions is a standard three-column layout. The gray regions are required and the rest are optional. `header`, `sidebar_first`, `sidebar_second`, and `footer` are layout regions. The `page_top` and `page_bottom` are special regions; they are discussed in the "Hidden Regions" section of this chapter.

**Figure 15-10**. Drupal's default layout for regions.

![Diagram of default regions laid out in three columns](http://themery.com/sites/default/files/figure-15-10.png)

The Highlighted region replaces the old Site Mission, which used to be a static variable containing the site's mission statement or a brief summary text that was output manually in `page.tpl.php`. The prior implementation was not ideal for a few reasons, but mainly because its display was limited to the front page. It was decided that using a custom block to display this information was a better option, so the highlighted region was created.

The Help region also used to be a `page.tpl.php` variable that printed error and status messages. The status messages are now displayed in a block called System help and the Help region was created to contain it. However, the System help block may easily be placed inside the Content region, weighted above the Main content block for the same effect.

The Content region is new to Drupal 7. It was introduced to contain the Main page content block, which is somewhat special because it can be moved from region to region but can't be disabled. Since the Block module is optional and the contents of the Main page content block are critical to operate a Drupal site, the contents of this block will always display via the `$page['content']` variable in `page.tpl.php`.

As a result, some of the Block module's functionality doesn't work as you might expect. If you place the Main page content block in the disabled area or set block visibility settings to exclude it from a page, the Block module's UI will lead you to believe that it has been disabled. However, the content will still appear. You'll also notice changes in the markup, which may lead to undesired results, such as un-styled content, depending on how your CSS is written.

## Hidden Regions

Notably missing from the options on the Blocks administration page in Figure 15–8 are the page_top and page_bottom regions. Both are hidden regions, which Drupal intentionally excludes from the user interface so that site administrators can't interact with or control their content. The main purpose of hidden regions is to act as a placeholder where modules or themes can dynamically add markup to in a structured way. Themes may declare hidden regions within .info files by using the following syntax, with each region on a separate a line:

regions_hidden[] = the_region_name

Both the page_top and page_bottom regions are printed in html.tpl.php (see Listing 15–4) and should not be removed or rearranged. The  page_top region, for example, is utilized by the Toolbar module to add the markup needed for the administrative toolbar shown at the top of each page when a user is logged in as a site administrator. The page_bottom region exists for modules to add any final closing markup, which specifically needs to be at the very bottom of the page. An example of this is the Google Analytics module, which adds markup to load JavaScript files that track the site visitor activity and needs to be loaded last. The page_bottom region replaces the $closure variable that was used in prior versions of Drupal.

Listing 15-4. The Contents of html.tpl.php, Highlighting the Placement of the page_top and page_bottom Regions.

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML+RDFa 1.0//EN" "http://www.w3.org/MarkUp/DTD/xhtml-rdfa-1.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="<?php print $language->language; ?>" version="XHTML+RDFa 1.0" dir="<?php print $language->dir; ?>"<?php print $rdf_namespaces; ?>>
  <head profile="<?php print $grddl_profile; ?>">
    <?php print $head; ?>
    <title><?php print $head_title; ?></title>
    <?php print $styles; ?>
    <?php print $scripts; ?>
  </head>
  <body class="<?php print $classes; ?>" <?php print $attributes;?>>
    <div id="skip-link">
      <a href="#main-content" class="element-invisible element-focusable"><?php print t('Skip to main content'); ?></a>
    </div>
    <?php print $page_top; ?>
    <?php print $page; ?>
    <?php print $page_bottom; ?>
  </body>
</html>
```

<blockquote><b>Tip:</b> Drupal uses <code>hook_system_info_alter()</code> to declare the <code>page_top</code> and <code>page_bottom</code> hidden regions. For more information, see <a href="http://api.drupal.org/api/function/system_system_info_alter/7">http://api.drupal.org/api/function/system_system_info_alter/7.</a>.</blockquote>

## Module-Specific Regions

The Dashboard module's Dashboard Main and Dashboard Sidebar regions are an example of regions created by a module. These regions are nontraditional in the sense that they can't be administered via the Blocks administration page, and the theme does not control defining or printing them. The Dashboard module defines them programmatically using `hook_system_info_alter()` and takes care of displaying them on the administrative Dashboard located at `/admin`. The Dashboard module allows you to drag and drop available blocks to those regions to create a dashboard for site administrators (see Figure 15–11).

![Screenshot of Dashboard module in edit mode, showing its use of custom regions](http://themery.com/sites/default/files/figure-15-11.png)
**Figure 15–11**. Administrative Dashboard in edit mode. 

## Regions and Your Theme

Getting started with your theme regions requires taking a good look at design requirements as well as planning for the unexpected. There are many things to consider, including how site administrators will need to work with blocks and regions, what types of content you have, and how regions play a part in your general layout strategy. As discussed earlier, the default regions are a great starting point. We recommend that you begin defining the defaults in your theme's `.info` file and tweaking from there, as shown in Listing 15–5.

**Listing 15–5**. Drupal's Default Regions

```ini
regions[page_top] = Page Top
regions[header] = Header
regions[highlight] = Highlight
regions[help] = Help
regions[content] = Content
regions[sidebar_first] = Sidebar First
regions[sidebar_second] = Sidebar Second
regions[footer] = Footer
regions[page_bottom] = Page Bottom
```

<blockquote><b>Tip</b> In addition to defining regions in your theme's <code>.info</code> file, you'll need to print them in the appropriate template file. The <code>page_top</code> and <code>page_bottom</code> regions print in the <code>html.tpl.php</code> template and the rest print in <code>page.tpl.php</code>. Printing regions and template files are discussed in more detail later in the chapter.</blockquote>

# Using Regions vs. Hard-coding Variables in Template Files    

When deciding whether or not to use regions in your theme, it's useful to consider the content that will be included in each section, how likely the position of the content is to change, and who needs to be able to change it. Blocks are flexible by nature and were designed to allow site administrators to easily move them around. This can cause problems if blocks are expected to be in a certain region and then moved or reordered.

When working on a site alone, or when only a few trusted individuals have control over the configuration of blocks, this is probably not something you need worry about. Alternatively, in cases where less trusted individuals have access and can potentially cause problems, taking extra measures to identify potential problem areas and doing what you can to prevent them is well worth it. For example, headers and footers are especially prone to this sort of problem. They usually have a tightly defined design and CSS to match. When blocks are moved around inside these regions, especially highly styled content such as the main menu navigation, things can go wrong quickly in the wrong hands. Sometimes defining an additional region, even if its purpose is to hold only one block, is a safer option compared with placing the block in the header region with other blocks. This will help ensure it is always printed in the right location and reduce the chance of user error. If site administrators do not need control over positioning, it may be best to print using a hard-coded variable in `page.tpl.php`, where it can't be affected by actions taken in the blocks interface.

As a general rule, consider using a region when content needs to be moved between regions or rearranged in the Blocks interface. When content doesn't need to be controlled via the Blocks interface, and it is risky for it to be there, consider hard-coding it in template files so it can't be affected by actions taken in the Blocks interface.

<blockquote><b>Tip:</b> The main menu (<code>$main_menu</code>) and secondary menu (<code>$secondary_menu</code>), which are located in <code>page.tpl.php</code>, are examples of hard-coded variables.</blockquote>

## Layout Strategies

The core defaults for sidebars (Sidebar First and Sidebar Second) were designed to handle multiple sidebar combinations with the help of body classes. Drupal is extremely flexible, and pages can be changed on a whim. Whether this will actually look good or not depends on how flexible and well coded the theme is. Since Drupal only prints regions that contain content, having a well planned and flexible layout is very important.

For example, let's say you have a two-column layout theme where the first column contains the main content and the Sidebar First region contains a single block. If you were to set the visibility of that block to only show on the home page, the entire Sidebar First region would only print on the home page and the inside pages would print just the main column. If your layout CSS only accounts for having both of those columns on each page, instead of including CSS for both a single column and the two-column, your layout will break. While regions are fairly easy to add or modify at any given time, oversimplifying the layout in the beginning of a project may come back to bite you in the form of extra CSS work. However many sidebars your theme will have, it's generally best to account for all possible sidebar combinations (one, two, or three columns) to avoid running into problems down the line. A great way to do this easily and sustainably is to use an established base theme.

There are also certain types of content that often work better in separate regions. For example, custom blocks containing advertisements and blocks that have significantly different design requirements are often easier to work with and write CSS for when they are abstracted. Figure 15–12 shows what adding a region for an ad banner and main navigation might look like.

It is also important to consider how the pages will be built and who will be working with them. If your site is going to be using regions and blocks to implement more complicated designs and you want to make it easy for site administrators to use, it may make sense to predefine multiple regions to lay out smaller sections of your pages. A good example of this is the Bartik theme, which contains seven additional regions to organize blocks in the footer, as shown in Figure 15–13. The same look could be achieved by defining two regions (Footer First and Footer Second) instead and style them using CSS to float the blocks in each to the left, but Bartik's implementation, shown in Listing 15–6 and illustrated in Figure 15–13, is arguably easier to understand for those who are not interested in the inner-workings of the code and just want to use the theme.

![](http://themery.com/sites/default/files/figure-15-12.png)
**Figure 15–12**. Example of custom advertisement banner and navigation regions.

**Figure 15–6**. Excerpt from Bartik Theme's `.info` File Where Its Seven Custom Regions Are Defined.

```ini
regions[triptych_first] = Triptych first
regions[triptych_middle] = Triptych middle
regions[triptych_last] = Triptych last
regions[footer_firstcolumn] = Footer first column
regions[footer_secondcolumn] = Footer second column
regions[footer_thirdcolumn] = Footer third column
regions[footer_fourthcolumn] = Footer fourth column
```

![](http://themery.com/sites/default/files/figure-15-13.png)
**Figure 15–13**. Populated footer regions in the Bartik theme.

## Exercise: Creating New Regions

The creation of a new region is a two-step process. Using the example in Figure 15–12, here's the process of creating the new Banner Ad and Navigation regions.


**Step 1:** Define regions in the `dgd7.info` file. Begin by adding the code for your new regions to the defaults you started with in Listing 15–3, plus the definition of the banner_ad and navigation regions to your `dgd7.info` file.

```ini
; DEFAULT REGIONS
regions[page_top] = Page Top
regions[header] = Header
regions[highlight] = Highlight
regions[help] = Help
regions[content] = Content
regions[sidebar_first] = Sidebar First
regions[sidebar_second] = Sidebar Second
regions[footer] = Footer
regions[page_bottom] = Page Bottom

; CUSTOM REGIONS
regions[banner_ad] = Banner Ad
regions[navigation] = Navigation
```

**Step 2:** Print the regions in the `page.tpl.php` template file. Once you clear your site caches, you'll be able to see and populate the new regions on the Blocks administration page at `admin/structure/block`. In order to get them to display on the page, you'll need to override the `page.tpl.php` file in your theme and print the new regions.

Navigate to the `modules/system` directory, copy the `page.tpl.php` file and paste it into the `sites/all/themes/dgd7` directory you created earlier.

Open the `page.tpl.php` file in the theme and scroll down to the <div id="page-wrapper"> and paste the code to print the region below it, and above the <div id="header">.

```html
<div id="page-wrapper">
  <div id="page">
    <?php print render($page['banner_ad']); ?>
    <div id="header">
      <div class="section clearfix">
```

Remove the default markup for the `$main_menu` and replace it with the region code for your new navigation region.

Remove this code:

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

Replace with this code:

```html
<?php print render($page['navigation']); ?>
```

**Step 3:** Technically you're finished, but let's add some content to illustrate what you've done.

Add a new custom block for the Banner Ad code. Title the block "Banner Ad" and add the following code to fake the appearance of an ad banner in the Block body (be sure to select the Full HTML text format). Then, select the Banner Ad region for the region settings and save it.

```html
<img style="width: 728px; height: 90px; border: solid 1px #000;" alt="728 x 90 Banner Ad" src="image.png">
```

Go back to the `admin/structure/block` page. Find the Main Menu block and place it inside the Navigation region and click Save blocks.

You've just added and populated two new custom regions!
