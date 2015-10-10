# Defining Theme Metadata (.info Files)


`.info` files (pronounced "dot info files") contain important metadata about your theme, such as the name of the theme, which version of Drupal it supports, as well as things like the style sheets and regions the theme will contain. Writing a `.info` file is usually the first step you take when creating a Drupal theme.

The first part of the file name is the machine-readable name of the theme, which Drupal uses to store information about your theme in the database. Dashes and other special characters are not allowed. While underscores are allowed, it is considered a best practice to avoid using them when naming your .info file. Use `.themename.info` instead of `theme_name.info`. This name will also be used to prefix function names when implementing theme function overrides. When overriding `theme_menu_link()`, for example, a function named `themename_menu_link()` is considered easier to read than `theme_name_menu_link()` when trying to determine the override being performed.

<blockquote><b>Caution</b>: Your theme (machine) name must be unique. Do NOT to give your theme the same name as any existing modules as it will likely cause namespace issues and make it difficult to track down PHP errors.</blockquote>

Each theme requires some basic properties to be set in the theme's .info file. The name, core, and engine properties are the bare minimum requirements for all Drupal themes. The following sections contain a brief description of each available property followed by an example of the syntax.

<blockquote><b>Tip:</b> To add comments to your `.info` file, add a semicolon to the beginning of each line, like so: `; This is a comment.` Comments are good. Make frequent use of them.</blockquote>

## Required Properties

### Core

Drupal will only allow your theme to be enabled if the core setting is set to support the current major version of Drupal. Major versions are simply 6.x, 7.x or 8.x, and so on.

```ini
core = 7.x
```

### Name

The human-readable name of your theme. It doesn't need to match or resemble the machine-readable name, so feel free to be creative here.

```ini
name = Theme Name
```

## Additional Properties

### Base Theme

Drupal allows themes to establish a relationship with each other. Creating a subtheme allows you to inherit the functionality and assets of the base theme (more on this in the next chapter). When creating a subtheme, you'll need to specify the base theme. It's important that the machine name of the base theme is used here.

```ini
base theme = themename
```

### Description

The basic features or purpose of the theme should be described here. The description will appear in the admin/appearance page and may contain HTML.

```ini
description = The description of my theme
```

### Engine

Specifies the theme engine. PHPTemplate is the default and most common, so unless you want to change this, it doesn't have to be manually set. Other options include smarty and theme for a pure PHP theme (see Chameleon for an example).

```ini
engine = phptemplate
```

### Featured

Setting features are a way of overriding Drupal's default global theme settings. The following is a list of the default theme settings provided by Drupal. These settings can be toggled on an off in the administrative interface on the Settings page of each theme. Specifying even one will disable Drupal's defaults and use yours.

```ini
features[] = logo
features[] = favicon
features[] = name
features[] = slogan
features[] = node_user_picture
features[] = comment_user_picture
features[] = comment_user_verification
features[] = main_menu
features[] = secondary_menu
```

### PHP

Drupal 7 supports PHP version 5.2.5, and by default, so does your theme. This is something you will probably never need, but in case your theme has code that only works with a certain version PHP, you may specify it here.

```ini
php = 5.3
```

### Regions

Regions are sections of your page layout where content (blocks) are placed. Each entry is prefixed with regions and contains the system name of the region in brackets with the human readable name as the value. For example, regions[system_name] = Human readable name. The default regions are as follows:

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

### Settings

The setting property is reserved for custom setting implementations in themes. The Garland theme provides a theme setting for the type of layout (fixed or fluid), which the site administrator can choose. While we won't be covering custom theme settings, we highly recommend checking out the Omega and Fusion themes to get an idea of how theme settings can be used. For more information, visit http://drupal.org/node/177868.

```ini
settings[garland_width] = fluid
```

### Screenshot

Drupal will automatically look for a file named `screenshot.png` in the root of your theme directory, so this line is only required if you want to use an alternative path or file name for your theme's screenshot. The recommended dimensions for the screenshot image are 294 x 219 pixels.

```ini
screenshot = screenshot.png
```

### Stylesheets

There are quite a few options for adding CSS files in Drupal 7. You'll want to add style sheets via the theme's `.info` for CSS files you want to load on every page. I'll cover this in much more detail in the Managing CSS Files section in the next chapter.

```ini
stylesheets[screen][] = path/to/screen-stylesheet.css
stylesheets[print][] = path/to/print-stylesheet.css
```

### Scripts

JavaScript files can be defined in .info files using the scripts property. Like style sheets, you'll only want to load JavaScript files here that need to be loaded on each page.

```ini
scripts[] = path/to/script.js
```

### Version

Specifying the version is discouraged for both contributed themes and modules. This is because drupal.org has a packaging script that takes care of adding the version when releases are created. However, you may use this for custom themes, if desired.

```ini
version = 7.x-1.1
```

Now let's see the basics in action by taking a look your DGD7 theme's `.info` file, as shown in Listing 15–1.

**Listing 15-1** The Top Portion of the DGD7 Theme's .info File.

```ini
name = DGD7 Theme
description = A theme written for The Definitive Guide to Drupal 7 book website.
core = 7.x
```

With the exception of the core property, all of the above can be seen in the user interface on the `admin/appearance page`, as shown in Figure 15–7. This is all you'll need to get started with your theme.

**Figure 15-7** DGD7 theme as shown on the theme listing page ``admin/appearance``.

![Screenshot of custom theme on Appearance page](http://themery.com/sites/default/files/figure-15-7.png)
