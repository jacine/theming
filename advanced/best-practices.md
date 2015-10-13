# Sustainability and Best Practices

Drupal contains many, many template files. For a front-end developer, these are one of your greatest tools in taking over a Drupal theme and turning it into exactly what you need it to be. However, with power comes responsibility. Because working with template files is so easy, it's also an area where you can get in trouble quickly.

Most front-end developers experience some frustration working with Drupal's markup. Because it's relatively easy to go in and make changes, doing exactly that is often a first reflex. Resist it. While you'll definitely feel the power and control you have over things, changing too many template files is often the wrong approach. Just because you can change things doesn't always mean you should.

## Start With a Good Base

A great way to ensure minimal template overrides is to define your markup in such a way that it is flexible enough to work in most cases. Think of the major template files like `node.tpl.php`, `views-view.tpl.php` and `block.tpl.php`, for example, as having two purposes. The first is to provide a container and the second is the actual content, which can include any number of different elements inside it. Drupal does this reasonably well to begin with, but there is always room for improvement, and your needs may vary from site to site depending on the design.

As an example, look at the contents of the `block.tpl.php` file, shown in Listing 16–44, which is provided by Drupal's Block module and can be found in `modules/block/block.tpl.php`. Most blocks, even those produced by other modules, will use this template file to output their contents. There could be a menu inside the block, a few paragraphs in a custom block, a snippet of JavaScript that will load an advertisement, a poll, a user listing, and so many other possibilities.

**Listing 16–44**. Default `block.tpl.php` implementation.

```html
<div id="<?php print $block_html_id; ?>" class="<?php print $classes; ?>"<?php print $attributes; ?>>
  <?php print render($title_prefix); ?>
  <?php if ($block->subject): ?>
    <h2<?php print $title_attributes; ?>><?php print $block->subject ?></h2>
  <?php endif;?>
  <?php print render($title_suffix); ?>
  <div class="content"<?php print $content_attributes; ?>>
    <?php print $content; ?>
  </div>
</div>
```

<blockquote><b>Tip:</b> The Bartik theme uses Drupal's default <code>block.tpl.php</code> template file. This is easy to determine because the Bartik theme does not include a <code>block.tpl.php</code> file in its directory.</blockquote>

Using a simple custom block as an example, the template code in Listing 16–44 translates to the output in Listing 16–45.

**Listing 16–45**. Block output using the default `block.tpl.php` implementation.

```html
<div id="block-block-1" class="block block-block first last odd">
  <h2>Block title</h2>
  <div class="content">
    <p>Block content.</p>
  </div>
</div>
```

The resulting code is pretty minimal. In most cases, when creating custom themes, you will not want these to look the same, so you will use CSS to style them differently. It may not be immediately apparent, but there are some potential problem areas to take note of with the default `block.tpl.php` implementation. Certain design aspects need more flexible markup. Some examples of this include:

- *Grids*: You may choose to lay out your blocks within regions using a CSS grid framework. This will prevent you from adding left and right padding/margins directly to the `.block` class.
- *Background images*: Your design might require adding multiple background images to achieve a design for the block that is content agnostic. Sounds easy enough, right? The top and tiling background image can be declared in `.block`, but where can the bottom background image be defined? As soon as you add padding to the `.block` class itself, you lose the ability to place the second background image on the existing .content class.

The previous examples are a small taste of what you might encounter while coding a Drupal theme. You may be tempted to take the minimalist markup approach and deal with problems as they arise, and this is where I would stop you! As mentioned, these main template files are responsible for containing many types of content. You don't want to create a new template file for every different kind just to modify structural aspects. It's much more sustainable, not to mention easier to code, to create solid and flexible defaults and deal with exceptions as they arise.

This can be achieved fairly easily by separating structure from content. As shown in Listing 16–46, by simply adding `<div class="inner">` to surround the contents, you can solve many potential problems before they arise. In the example of grids, padding can be applied to the `<div class="inner">`. As for background images, the top background image can be applied to `.block`, and the bottom can be applied to `.block .inner` or vice versa.

**Listing 16–46**. Modified `block.tpl.php` to contain a more flexible container structure.

```html
<div id="<?php print $block_html_id; ?>" class="<?php print $classes; ?>"<?php print $attributes; ?>>
  <div class="inner">
    <?php print render($title_prefix); ?>
    <?php if ($block->subject): ?>
      <h2<?php print $title_attributes; ?>><?php print $block->subject ?></h2> <?php endif;?>
    <?php print render($title_suffix); ?>
    <div class="content"<?php print $content_attributes; ?>>
      <?php print $content; ?>
   </div>
  </div>
</div>
```

## Override Template Files with Purpose

While core template files are less likely to change during the course of a major release cycle, there are usually massive changes to template files for each major Drupal release, and contributed modules are a constantly moving target. Template files can change at any time, and sometimes drastically. There are many potential reasons for these changes. A module developer may decide to take a different approach, there might be new features or security updates, or there may be no good reason at all. The point is that once you override a template file by adding it to your theme, you are responsible for maintaining it. This can easily get out of hand if you have too many template files.

Another thing to remember is that Drupal is a framework. The whole idea of using Drupal is to take advantage of its modularity. Having too many template files in your theme can essentially remove that modularity; once that happens, your theme can become more of a hassle to maintain than all of Drupal and whatever custom modules you have combined. The key to avoiding this problem is to use overrides sparingly and take advantage of the many tools that Drupal provides.

Just adding the `<div class="inner">` as you did in Listing 16–46 can go a long way in saving you the need to create additional template files. The following tips will help you stay out of trouble when working with templates in Drupal themes:

- *Structure for the majority*. Explore options for handling one-offs separately by using preprocess functions where possible.
- **Take advantage of theme hook suggestions**. When the differences between the markup warrant it, use `node--article.tpl.php` to style article nodes and use `theme_links__node()` to target only node links.
- *Take advantage of CSS classes as arrays*. If all you need is a class, don't create a new template file. For example, block titles are output in a simple `<h2>` tag by default. When applying even minimal CSS to `.block h2`, you run the risk of affecting `<h2>` tags that may end up inside `<div class="content">`. Add a class to the title to style against, so you can prevent these issues.

## Leverage Default CSS Classes

Don't just rip out or change CSS classes without good reason for doing so. Think about it. While many front-end developers and web designers gasp at the sight of all the CSS classes that Drupal makes available, there really is a purpose to the madness. These classes (especially body classes) not only provide helpful information that guides you through figuring out what is generating the markup and what characteristics the contents of a given `<div>` might have, but they are designed to give you the opportunity to do a large portion of your theme development within the CSS.

Keep in mind, especially when using contributed modules, that you will need to update and likely upgrade your site at some point in the future, and that you can't control the changes that may be made to templates and often to the classes applied inside them. It's also important to note that modules may rely on classes and certain CSS files, such as `system.base.css` to be loaded in order to function properly. Of course, you can try to manage these things, but we can report from experience that this can easily turn into a frustrating waste of time. We're not saying there's not room for improvement or that you shouldn't code your site the way you want to. We simply want to make you aware of some of the risks involved when stripping markup down to barebones.

## Do My Changes Belong in a Module?

With each new release of Drupal, the theme layer becomes more and more powerful. With the advent of Render API and the ability to use alter hooks in themes, Drupal 7 is packed with more power than ever. As powerful as Drupal themes can be, there are still many things that just do not belong in the theme layer. As you are plugging away coding your awesome Drupal theme, constantly ask yourself these questions:

- Does what you are trying to accomplish require an SQL query? These should never be in a theme. Period.
- Does your task seem particularly difficult to accomplish?
- Are you completely rebuilding data?
- Are your changes really theme-specific? For example, if you are changing form labels and descriptions, should these be available if you were to disable your theme?

If the answer to any of these questions is yes, then your changes belong in a module.
