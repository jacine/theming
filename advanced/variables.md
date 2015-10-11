# Finding Available Variables in the Theme Layer

When working in the theme layer, you'll find that the variables are different depending on the type of entity with which you are working. You'll also find that the various templates and theme functions don't use or document all of the variables that are available, so one of the things you'll often need to do is print the contents of arrays to the screen.

There are various ways to print arrays using PHP. One of the most common ways is to use the `print_r()` function. There's also `var_dump()`, `get_defined_vars()` and Drupal's own `debug()`. These functions are great for small arrays, but Drupal's arrays are known for being tremendous, thus using these functions while coding the front end of a site is annoying, to say the least. Luckily, thanks to the Devel module and the Krumo library, printing compact and easily readable arrays is a piece of cake. Upon installing the Devel module, you'll have access to functions like `dpm()` and `kpr()` among others.

When working with templates and preprocess functions, you'll usually print `$variables` using `dsm()` or `dsm()`. As an example, try adding `<?php dpm($variables); ?>` to the top of your `node.tpl.php` file.

![Screenshot of dpm() output](http://themery.com/sites/default/files/figure-16-1.png)

**Figure 16–1**. The result of printing `<?php dpm($variables); ?>` in `node.tpl.php`.

In Figure 16–1, you see the result of printing the contents of the `$variables` array using the `dpm()` function. What's nice about using `dpm()` is that the array is neatly printed using the $messages variable in page.tpl.php, which is where system status messages are located. As shown in Figure 16–2, you can click the heading and expand the contents of each part one by one.

![Screenshot of expanded dpm() output](http://themery.com/sites/default/files/figure-16-2.png)

**Figure 16–2**. An expanded array printed using the `dpm()` function.

When working inside template files, these variables are made available as top-level variables. This is done as a convenience for theme developers. For example, instead of printing `$variables['status']`, just print `$status` in templates. When working inside functions, such as theme functions or preprocess functions, use `$variables['status']`.

## Using the Theme Developer Module    

Of course, when you're first starting out with Drupal, you'll need to get an idea of where the code is located and what you need to override in the first place. The [Theme Developer](https://www.drupal.org/project/devel_themer) module is the perfect tool to help you figure this out. Once enabled, a checkbox will appear in the bottom right corner of the page. When clicked, a semi-transparent, resizable, and draggable window appears in the top right corner of the page. You can then move it around and click on any element of the page and the window will populate with all the information you need to know and more (see Figure 16-3).

For example, when clicking a node, the following information is made available in the window:

- The parent functions and templates that affect the element
- The template or theme hook suggestions (candidates)
- The preprocess and process functions being used
- A printout of the variables available

![Screenshot of Theme Developer module output](http://themery.com/sites/default/files/figure-16-3.png)

**Figure 16–3**. The Theme Developer window shows theme-related information about the element that was clicked (a node in this case).
