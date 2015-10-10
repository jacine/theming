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

## Module-Specific Regions

## Regions and Your Theme

## Using Regions vs. Hard-coding Variables in Template Files

## Layout Strategies

## Exercise: Creating New Regions