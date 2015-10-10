# The Core Themes Directory

When starting out, one of the first things people do is navigate to the core `/themes` directory and take a look at the files in the themes to get an idea of the general structure and contents. Unfortunately, many people make the mistake of starting out by directly customizing core themes. Do not make this mistake! They usually run into roadblocks and frustration shortly thereafter. Drupal has a large and diverse user base, and the main goal of a core Drupal theme is to cater to the masses.

Aesthetics aside, core themes have many requirements and different use cases to satisfy. A few themes support the Color module in order to make it easy for site administrators to change color schemes in the user interface. This is not a bad thing; however, it can easily become confusing and frustrating when trying to customize colorized themes because CSS is generated programmatically and stored outside of the theme directory. Core themes must also function if used as an administration theme and they must support bidirectional text; in general, they can't stray far from Drupal's default regions and settings.

It's not easy to please everyone, and Drupal core themes have the tough job of trying to do just that. As a result, core themes are nowhere near as flexible or as cutting edge as they could be. Most of the time, your goal and approach will be very different when creating custom themes. You'll be able to focus on coding your own front-end or back-end focused design, customize the markup, decide which CSS files to use (if any), and other exciting decisions.

## Core Themes

Drupal core contains four themes. They are introduced in the following sections.

### Bartik

Bartik is a new and welcome addition to Drupal 7. Drupal enables Bartik as the default user-facing theme upon installation. It is a clean and simple theme that supports the color module and makes excellent use of regions (see [Figure 15–1](#figure-15-1)). In addition to the default regions Drupal recommends, the Bartik theme has seven custom regions for laying out blocks in the footer and sub-footer.

![Screenshot of Bartik](http://themery.com/sites/default/files/figure-15-1.png)
**Figure 15–1.** Bartik is a clean and simple theme.

### Garland

Garland originally made its debut as a core theme in Drupal 5. It is a more complex theme with excellent color module support (see [Figure 15–2](#figure-15-2)). It contains fifteen color schemes and provides an option to toggle between a fixed or fluid layout.

![Screenshot of Garland](http://themery.com/sites/default/files/figure-15-2.png)
**Figure 15–2.** Garland is a more complex theme with excellent color module support.

### Seven

Also new to Drupal 7, Seven is Drupal's default administrative theme. Born out of the [Drupal 7 User Experience project](http://d7ux.org), Seven drove many of Drupal's user interface improvements. It contains very few regions, as its focus is on performing administrative tasks (see [Figure 15–3](#figure-15-3)).

![Screenshot of Seven](http://themery.com/sites/default/files/figure-15-3.png)
**Figure 15–3.** Seven is Drupal's default administrative theme.

### Stark

Stark is a unique and literally minimal Drupal theme (see [Figure 15–4](#figure-15-4)). Its main purpose is to expose Drupal's default HTML markup and CSS. It does not provide any template files and barely provides any CSS at all, other than basic layout styles that place the default sidebar regions. Don't let its simplicity fool you; it is actually quite useful. Stark is the perfect theme for developers to code against when writing CSS for their modules. It can also assist theme developers when trying to troubleshoot issues where they're not positive if the problem is with their theme or another module.

![Screenshot of Stark](http://themery.com/sites/default/files/figure-15-4.png)
**Figure 15–4.** Stark is a unique and literally minimal Drupal theme.

## Theme Engines

Drupal's theme directory also has an engines directory that contains a theme engine called PHPTemplate. Theme engines provide an easy way to separate themable output into template files as opposed to plain old PHP. The main benefit of using the PHPTemplate engine is that separating logic from presentation is simplified. Those who are unfamiliar with PHP can accomplish a great deal because they are able to work in template files that mainly contain markup and print variables.

While other theme engines such as Smarty, XTemplate, and PHPTal may be used, PHPTemplate is Drupal's default theme engine and is by far the most popular theme engine used by Drupal themes (and many popular contributed modules), so we will cover it in this chapter. It is also possible to write pure PHP Drupal themes. See the Chameleon theme for an example of a pure PHP theme at [http://drupal.org/project/chameleon](http://drupal.org/project/chameleon). For a full listing of available theme engines, visit [http://drupal.org/project/theme+engines](http://drupal.org/project/theme+engines).
