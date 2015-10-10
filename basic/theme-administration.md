# Theme Administration

Theme configuration tasks are located in the Appearance section of Drupal's administration. This is where you can control things like which themes you want to enable or disable, which settings you want to apply, which color scheme you want to use (if your theme supports the color module), and more.

## Enabling and Setting a Default Theme

In a fresh installation of Drupal 7, the default theme (Bartik) appears at the top of the Appearance page, followed by other enabled and disabled themes (see [Figure 15–5](#figure-15-5)). What is a default theme? Simply enabling a theme is not enough with Drupal. Setting a theme as the default is what makes it the front-end theme (the theme your site visitors will see).

![Screenshot of the appearance page with notes explaining enabled vs. default themes](http://themery.com/sites/default/files/figure-15-5.png)
**Figure 15–5.** The Appearance page in a default installation showing enabled themes.

Enabling a theme without setting it as the default is useful when you want your site to utilize multiple themes at once. This setting is typically more useful when used in conjunction with contributed modules. An example of this is the [SwitchTheme](http://drupal.org/project/switchtheme) module, which allows users to change the site theme by selecting the name of a theme from a list that is populated with all enabled themes.

## Administration Theme

In Drupal 7, the Seven theme is the default administration theme. The administrative theme is used when performing all administrative tasks, most of which happen under the `admin` path. You can also choose to allow the administrative theme to be used when editing site content. Although some themes support Drupal's administrative interface better than others, any Drupal theme can be used as an administration theme if desired.

The administration theme's configuration settings are located below the theme listings on the `admin/appearance` page. To use the same theme in both the front and back end of your Drupal site, simply choose Default theme as the Administration theme.

## Global Theme Settings

Drupal comes with some theme settings that can be configured in the administrative interface. This is where most of the site identity assets are defined, as well as a couple of other miscellaneous settings. A Global Settings page located at `admin/appearance/settings` contains these settings. When global settings are saved, the settings apply to all themes. Each theme also has its own settings page accessible via a settings link located next to each enabled theme on the `admin/appearance` page. When theme settings are applied on an individual theme's Settings page, they override the global settings. The following sections will detail what each of these are and where you'll encounter them in your themes.

Quite a few of these settings determine whether or not variables are populated and therefore printed in template files. The settings pictured in [Figure 15–6](#figure-15-6) represent the defaults provided by Drupal. These can be overridden by themes by defining features in the theme's `.info` file, which is discussed further in the _[Defining Theme Metadata](/node/67)_ section. When specifying features in `.info` files, you'll need to make sure you include all the features you want to support, as having just one will override all of the defaults provided by Drupal. The following is a quick reference of these settings as they'd be entered in a `.info` file:

```ini
features[] = logo
features[] = name
features[] = slogan
features[] = favicon
features[] = main_menu
features[] = secondary_menu
features[] = node_user_picture
features[] = comment_user_picture
features[] = comment_user_verification
```

![Screenshot of Global theme settings](http://themery.com/sites/default/files/figure-15-6.png)
**Figure 15–6.** The Global Settings page.

### Logo

By default, Drupal will look for a file named `logo.png` in the root of the theme directory. There is also an option to specify a path to a different file to use for the logo, as well as the ability to upload a logo to use. When the Logo checkbox is checked, a variable called `$logo` is populated with the path to the logo, which will be available in `page.tpl.php`. If unchecked, the logo will not print.

### Name and Slogan

The site name is defined during the installation process. Both the site name and slogan can be changed on the `admin/config/system/site-information` page. On the theme's Settings page, you can toggle their visibility. Both are available in `page.tpl.php` as `$site_name` and `$site_slogan`.

### Shortcut Icon

The shortcut icon, also known as the favicon, is the small Drupal icon that appears in the address bar, bookmarks, and tabs of most browsers. Like the logo, the shortcut icon's visibility can be toggled and a custom file can be used. The default file is `misc/favicon.ico`.

### User Pictures in Posts and Comments

These settings control whether or not the variables `$user_picture` in `node.tpl.php` and `$picture` in `comment.tpl.php` are populated, and therefore whether or not the pictures are displayed when viewing nodes and comments.

### User Verification Status in Comments

This option will display "(Not verified)" next to the user name for users that do not have a verified account. This text is defined in `template_preprocess_username()` and printed in `theme_username()` as `$variables['extra']`. See the "Preprocess and Process Functions" and "Theme Functions" sections to learn how to change this.

### Main and Secondary Menus

When the checkboxes for the Main and Secondary menus are checked, `$main_menu` and `$secondary_menu` variables are populated in `page.tpl.php` with arrays containing the menu links for each menu. On the Menu Settings page, located at `admin/structure/menu/settings`, you can choose which menu is used for each. By default, the Main menu, which can be managed at `admin/structure/menu/manage/main-menu`, is used as the source that populates `$main_menu`. The default menu for the source of the Secondary menu is the User menu, which can be managed at `admin/structure/menu/manage/user-menu`.

These are simple one-level menus output using the `theme_links()` function (which will be covered later in this chapter) in `page.tpl.php`. This makes them hard to use when styling complex navigation designs. Because complex navigation is often required, many theme developers create regions for navigation and use blocks to output their menus instead of using these menus. We highly recommend the Menu Block module, which allows you to do pretty much anything you'll ever need to do with menus very easily.

### Custom Theme Settings
Custom theme settings are similar to the global theme settings and can be provided by themes and modules. An example of custom theme settings can be found in the Garland theme in the `garland.info` file. It creates a setting called `garland_width` that can be set to fixed or fluid. The Shortcut module also provides a setting to display the "Add or remove shortcut link" used in the Seven theme to provide the icon you see in the Overlay next to the title. To learn how to create custom theme settings for your theme, visit http://drupal.org/node/177868.

## Installing a New Theme    

Drupal scans its theme directories for available themes, so it's important that you place your themes in the right place for Drupal to recognize them. You might also be tempted to add themes to Drupal's `/themes` directory, but technically this is considered "hacking core" and should be avoided. After downloading and unpacking your theme, choose one of the following directories in which to place the theme. Using one of these directories will help ensure that any updates you make to Drupal itself will not result in accidentally overwriting your theme.

<dl>
  <dt>sites/all/themes</dt>
  <dd>Use this directory when you want the theme to be available to all sites in your Drupal installation.</dd>
  <dt>sites/sitename/themes</dt>
  <dd>Use this directory when you only want the theme available to a specific site in your multisite Drupal installation.</dd>
</dl>

You may also use the theme installer to download and install contributed themes by clicking the Install new theme link at the top of the Appearance page. This will bring you to a form where you can enter the link to the project download's tarball location and click Install. The theme installer will automatically download your theme and place it in the `sites/all/themes` directory. Once completed, you can enable the theme as usual on the `admin/appearance` page.
