# Working with Regions

Most of the content found on Drupal pages is output inside a region. Typical regions include the header, footer, sidebars, and content (see Figure 15–8); these regions often play a large part in defining the high-level structure of your HTML markup. An option appears in the blocks interface at admin/structure/block for each region, allowing site administrators to control and position the blocks inside them.

**Figure 15-8**. The Bartik theme's regions and block placement options on the Blocks administration page.

![Screenshot of Blocks UI showing placement in regions](http://themery.com/sites/default/files/figure-15-8.png)

Themes have full control over defining and determining the placement of printing and styling regions. An example of what this looks like in the Bartik theme is shown in Figure 15–9.

**Figure 15-9**. Bartik regions filled with custom blocks.

![Screenshot of Bartik regions filled with custom blocks](http://themery.com/sites/default/files/figure-15-9.png)

In addition, themes may also use regions for less obvious purposes in combination with JavaScript or jQuery. Common use cases for regions include containing modal or hidden content to enhance the user interface or embedding blocks into node content.
