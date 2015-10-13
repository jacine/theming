# Working with Base and Subthemes

Chances are you have a certain way you do things. You may tend to structure your markup similarly in all your themes. You might frequently override certain theme functions, or have special way you like to style forms, or maybe you tend to use a certain grid framework for your layout. These are all great reasons to take advantage of Drupal's base and subtheming functionality.

Subthemes share a special relationship with their base (parent) theme(s). They inherit template files and assets from their parent themes. This makes them a great tool to help streamline your theming workflow and essentially create your own "frameworks" or "resets" for theming Drupal sites. Of course, you can also use an existing base theme. Drupal offers quite a few base themes, which we'll tell you more about later in this section.

## Creating a Subtheme

Both base and subthemes are regular Drupal themes as far as characteristics go, and any theme can be a base theme. The process of creating a subtheme is very straightforward.

Start by creating the shell of a new theme. Create a directory for it, and create the .info file containing at least the name and core properties.

In the `.info` file, add the "base theme" property containing the name of the theme you want to use as a base, like so:

```ini
base theme = basethemename
```

If the base theme has regions and/or features defined in the `.info` file, you'll need to copy those to the subtheme as well.

For basic Drupal themes, these three steps are all you'll need to do to create your subtheme. Once you've done this, you'll be able to enable the theme on the `admin/appearance` page. It's also worth noting that the base theme you are using does not need to be enabled in the UI to function properly.

<blockquote><b>Caution:</b> Most of the popular contributed base themes require a little more to set up. Themes like Zen, Omega, and Fusion come with a starterkit or starter directory, which you can copy and use to start your subtheme. Make sure you refer to each theme's <code>README.txt</code> file for full instructions on how to begin using it, as each is different.</blockquote>

## Inheritance and How it Works

You already know that Drupal provides a lot of markup in its modules, and that this markup comes in the form of templates, theme functions, or the Render API. In Drupal themes you have the opportunity to override and take over this behavior. So, technically, you are inheriting it in the first place. Using subthemes allows you to add one more step to the process. When using a parent theme, all of the assets — including template files, CSS files, JavaScript files, theme functions, and pretty much everything in `template.php` — are inherited.

CSS, JavaScript, template files, and theme functions defined in a base theme will automatically be available for the subtheme. The subtheme doesn't have to do anything for this to happen. It just works. Preprocess and process functions will run for both the base and the subtheme, so they can be used in both themes simultaneously without issue. Of course, the subtheme can override anything the base theme has done.

Some things don't work this well. Regions are not inherited, and neither are features or theme settings. In order for these to work properly, you'll have to copy the information from the base theme into the subtheme's `.info` file. Table 16–4 shows which assets are automatically inherited and which ones are not.

**Table 16–4**. Inheritance of assets from base theme to subthemes.

Asset | Automatically Inherited?
:---- | :-----------------------
CSS files | Yes
JavaScript files | Yes
Template files | Yes
Theme Screenshot | Yes
Regions | No
Theme Settings | No

## Finding a Good Base Theme

Thousands of contributed themes are available at http://drupal.org/project/Themes. Unfortunately, Drupal themes have a reputation for being ugly. While there is some truth to that, there are many gems out there; you just need to know what to look for. Themes on drupal.org are sorted by popularity, based on project usage stats, so it is easy to see which themes are the most popular. However, popularity is not always the best measure. There are a few things you should understand when evaluating a contributed Drupal theme.

#### Type

All of the themes on drupal.org are lumped together into one, uncategorized list. As you can see on http://drupal.org/project/themes, a large portion of the themes on the first page are base themes. While any theme can technically be used as a base theme, it's important to read the project information so you know what to expect. Maintainers will be a lot less inclined to help you with a problem if you're not using the theme how they intended.

#### Maintenance and development status

Each project has a Maintenance and a Development status which can be viewed on the project page. These will give you a good idea of how the module is supported. If the project has an "Actively maintained" maintenance status and an "Under active development" development status, chances are that the module developer intends to fix bugs and will entertain feature requests made in the issue queue.

#### Usage statistics

On each project page, the project Information section contains the number of reported installations and a link called "View usage statistics" that shows a long term graph and table of this data and how it has changed over time. Usage statistics can be a good indication of whether or not a theme has been well tested. If many people are using it or it shows steady growth, chances are that it's a better theme.

#### Issue queue

Most projects contain issues queues where users can report bugs and request features. Reading through the issue queue is a good way to gauge the community participation in a project. It is also a great way to learn what bugs the theme may have and how quickly the community and maintainer(s) respond to such issues.

### Popular Base Themes

There are many great base themes available from seasoned theme developers on drupal.org. A comprehensive list of available base or "starter" themes is available at http://drupal.org/node/323993. Some of the most popular base themes available for Drupal 7 include:

- Zen
- Fusion
- AdaptiveTheme
- Genesis
- Basic
- Blueprint
- NineSixty
- Omega
- Mothership


## Tips for Creating Your Own Base Themes

- *Don't do too much*: It's important not to make too many assumptions in your base themes. Ask yourself if what you are doing will fit in well on any project you work on. If the answer is no or maybe, it's likely not a feature you should include in your base theme.
- *Look at contributed themes*: Looking at what other contributed themes have done is one of the best ways to learn. Chances are you'll find some things you like and some things you don't from each of them. Don't be afraid to mix and match.
- *Provide styles for layout and others structural elements*: Take care of things that you consistently do on each project. For example, normalize font sizes, provide CSS resets, and make sure that the general padding and margins are set so blocks and nodes are not on top of each other.
- *Use multiple CSS files*: Aggregation and compression will take care of combining these files automatically, so don't be afraid of using a few CSS files. This will allow you to easily choose between what you want and don't want in your subthemes.
