# Defining Theme Metadata (.info Files)


`.info` files (pronounced "dot info files") contain important metadata about your theme, such as the name of the theme, which version of Drupal it supports, as well as things like the style sheets and regions the theme will contain. Writing a `.info` file is usually the first step you take when creating a Drupal theme.

The first part of the file name is the machine-readable name of the theme, which Drupal uses to store information about your theme in the database. Dashes and other special characters are not allowed. While underscores are allowed, it is considered a best practice to avoid using them when naming your .info file. Use `.themename.info` instead of `theme_name.info`. This name will also be used to prefix function names when implementing theme function overrides. When overriding `theme_menu_link()`, for example, a function named `themename_menu_link()` is considered easier to read than `theme_name_menu_link()` when trying to determine the override being performed.

Caution Your theme (machine) name must be unique. Do NOT to give your theme the same name as any existing modules as it will likely cause namespace issues and make it difficult to track down PHP errors.

Each theme requires some basic properties to be set in the theme's .info file. The name, core, and engine properties are the bare minimum requirements for all Drupal themes. The following sections contain a brief description of each available property followed by an example of the syntax.

Tip To add comments to your `.info` file, add a semicolon to the beginning of each line, like so: `; This is a comment.` Comments are good. Make frequent use of them.
