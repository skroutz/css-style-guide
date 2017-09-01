# CSS Architecture and Coding Style Conventions

> When it comes to web development, <br/>
> the answer to most questions is “it depends”.<br/>
> -- Snooca

Welcome to [Skroutz](http://www.skroutz.gr) CSS Styleguide. Here are some techniques that help us in keeping CSS organised and structured, leading to code that is both easier to read and maintain.

This is the guide we use internally, for our own apps, at [Skroutz](http://www.skroutz.gr).

This guide contains a mashup of ideas from the [Github CSS styleguide](https://github.com/styleguide/css), the [Scalable and Modular Architecture for CSS guide](http://smacss.com/) (SMACSS for short) with some sprinkle from the [Atomic Design methodology](http://bradfrost.com/blog/post/atomic-web-design/).

## Table of Contents

* [Coding Style](#coding-style)
  * [Formating](#formating)
  * [Comments](#comments)
* [Coding Preferences](#coding-preferences)
  * [Pixels vs. Ems](#pixels-vs-ems)
  * [Variables](#variables)
* [Specificity](#specificity)
  * [CSS Specificity guidelines](#css-specificity-guidelines)
* [Media Queries](#media-queries)
* [Declaration Order](#declaration-order)
* [Atomic approach](#atomic-approach)
* [File Organization](#file-organization)
* [Misc](#misc)

## Coding Style

### Spacing

* Use soft-tabs with a two space indent.
* Put spaces after  colon `:` in property declarations.
* Put spaces before curly bracket `{` in rule declarations.
* Put spaces after comma `,`.
* Seperate rules by new lines.

### Formating

* Use a semicolon `;` after every declaration. Even the last one.
* Don't use camelCase or underscores in class names. Use dashes.
* Properties within rule sets should each reside on their own line.
* Use hex color codes `#000` instead of using rgb.
* Ideally try to use color variables from skroutz color palette instead of hex or rgb.
* Use shorthand properties where possible.
* When grouping selectors, keep individual selectors to a single line.
* Avoid specifying units for zero values, e.g., `margin: 0;` instead of `margin: 0px;`.
* Prefer `border: 0` over `border: none`.
* Prefer `color: #fff` instead of `color: white`.
* Strip out the zero for decimal number, prefer `.5s` over `0.5s`.
* Try to use single `'` quotes instead of double quotes`”`. In rare situtations where both needed inner quotes should always be single.
* Attributes selector should always be enclosed within quotes (Bad example: `[type=submit]`, Good example: `[type='submit']`
* Set a limit of 80 characters width at CSS files.
* try to set a max nesting limit of 3 levels.
* Use `//` for comment blocks instead of `/* */`.
* Always include mixins at top of any other css properties.
* Always add an empty line between nesting block (even if it hasn’t any properties).
* Parentheses should not be padded with spaces, also try not to add a space after mixin name (Bad example: `@include animation ( blur 1s linear )`, Good example: `@include animation(blur 1s linear)`).


Examples:

#### Good

```SCSS

.foo {
  border: 1px solid $grey5;
  color: $blue3;
  background: rgba(0, 0, 0, .5);
}

.foo {
  @include center(vertical);
  color: $orange;
  font-size: 13px;

  &:hover {
    color: $red;
  }
}
```

#### Bad
```SCSS

.foo_bar {
  border: 1px solid #000;
  color: #000;
  background: rgba(0, 0, 0, .5);
}
.fooBar {
  @include center(vertical);
  color: $orange;
  font-size: 13px;
 &:hover {
    color: $red;
  }
}
```



### Comments

Try not to use CSS comments, instead use SASS comments for your comment block. We separate comments into 3 categories: descriptive, block titles and hints.

#### Descriptive comments

Descriptive comments are usually needed to provide more informations about a css block. So they should always appear in a new line above the code block, and should always follow by a new line as well.

```SCSS

// If supports line-clamp then add an ellipsis overflow and hide the gradient
// This will work in Chrome and latest Opera, otherwise a gradient will
// gradually hide the text.

.foo {
  ...
}
```

#### Block Title comments

Block titles are used to organise style blocks according to their content. Should always follow by a new line right bellow and contain 2 new lines above them.

```SCSS
.foo {


  // bar styles

  .bar {
  ...
  }
}
```

#### Hints comments

Hints comments are usually needed to provide a short hint for a specific propertie. Hints comments should always appear in the same line with the css properties.

```SCSS
.foo {

  &:before {
    content: ‘\805’; // Icon star
    ...
  }
}
```

## Coding Preferences

### Pixels vs. Ems

Use `px` for `font-size`, because it offers absolute control over text. Additionally, unit-less `line-height` is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the `font-size`.

### Variables

Sass variables can be extremely powerful and helpful. Think of variables as a way to store information that you want to reuse throughout your stylesheet. You can store things like colors, fonts, or any CSS value you think you'll want to reuse throughout the site.

* Define color variables in `helpers/_variablies.scss`.
* Use the color variables and don't use hardcoded hex triplets directly in CSS files.
* Define font variables in `global/_fonts.scss`. Currently, we mainly use `Verdana`, but you can also use `Georgia` and `Arial`.
* Try to use semantic names for your variables (Good examples: `$black: #000;`, `$arial: Arial, Helvetica, sans-serif;`).
* Try to use colors that we already use and if you want to use one that we don’t, you can add it in variables as long as we will need it again.
* If there is a variable that you want to use in a specific file and we don’t use it in general, define it at the top of the file (ex. `$facebook: #4363ac;` in `atoms/_buttons.scss`)

```SCSS
// a BAD example

$active: #333;
$width: 10px;

// a GOOD example

$active-link-color: #333;
$border-width: 10px;
```

## Specificity

> CSS takes into account both code order and selector specificity.<br/>
> So, don't fight the css cascade.

Elements that occur **exactly once** inside a page should use IDs, otherwise, use classes. When in doubt, use a class name.

  * **Good** candidates for ids: header, footer, main.
  * **Bad** candidates for ids: navigation, item listings, buttons.

When styling a component, prefer class names over ids, prefer direct descendant selectors by default, and use as little specificity as possible. Here is a good example:

```SCSS
.category-list {

  > li {
    list-style-type: disc;
  }

  a {
    color: $grey;
  }
}
```

### CSS Specificity guidelines

* If you must use an id selector (`#selector`) make sure that you have no more than one in your rule declaration. A rule like `#header .search #quicksearch { ... }` is considered harmful.
* Never reference js- prefixed class names from CSS files. js- are used exclusively from JS files.
* When modifying an existing element for a specific use, try to use specific class names. Instead of `.listings-layout.bigger` use rules like `.listings-layout.listings-bigger`. Think about `ack/grep`ing your code in the future.
* The class names `disabled`, `mousedown`, `danger`, `selected`, and `active` should **always** be namespaced by a class (`button.selected` is a good example).

#### ID selectors

Using ID attributes in our HTML can be a good thing and in some cases, absolutely necessary. For example, they provide efficient hooks for JavaScript. For CSS, however, ID selectors aren’t necessary as the performance difference between ID and class selectors is nearly non-existent and can make styling more complicated due to increasing specificity.

#### Avoid element selectors

Use child or descendant selectors with element selectors if the element selectors will and can be predictable. Using `.class` span is great if a span will predictably be used and styled the same way every time while within that module. The problem is that as a project grows in complexity, the more likely that you will need to expand a component’s functionality and the more limited you will be, having used such a generic element within your rule.

If you do wish to use an element selector, it should be within one level of a class selector. In other words, you should be in a situation to use child selectors.

#### Only include a selector that includes semantics

A span or div holds none. A heading has some. A class defined on an element has plenty. By adding the classes to the elements, we have increased the semantics of what those elements mean and removed any ambiguity when it comes to styling them.

If you do wish to use an element selector, it should be within one level of a class selector. In other words, you should be in a situation to use child selectors.

## Media Queries

> The point is that you want to have a system that is responsive.<br/>
> -- Bill Joy

Media queries are an approach to managing state change. A media query, should be defined within a @media block within a specific style sheet (at the bottom).

The intent is to keep the styles that pertain to a specific layout or module in a logically grouped way. Since we use a mobile first approach we declare 2 redlines for three versions in our websites, 640px and 960px:

* Viewport < 640px (Default, Mobile version).
* 641px < Viewport < 960px (Medium, Tablet version).
* 961px < Viewport (Large, Desktop version).

Media queries suffixes we use, are the following:

* _large.scss (Desktop version).
* _medium.scss (Tablet version).

We also use media queries to distinguish between [HiDPI displays](http://en.wikipedia.org/wiki/Retina_Display) (aka. Retina displays) and normal displays:

* _retina.scss (Retina version).

## Declaration Order

The most popular way to order CSS properties is grouped by type. Our preference is for structurally important properties (e.g. positioning and box-model) to be declared prior to all others.

## Atomic approach

To build things faster, better, and easier, we needed to design and develop a robust system of components. We decided to design our design system, which would consist of typography, colors, components, layouts, conventions and clean design guides, in a way that would allow our entire team to build things fast and in a consistent way.

[Atomic Design](http://bradfrost.com/blog/post/atomic-web-design/), [Material Design](https://material.io/guidelines/material-design/introduction.html), [Card-based Design](https://thenextweb.com/dd/2015/06/16/how-cards-are-taking-over-web-design/#.tnw_BPUUhY0H) and more, inspired us in creating and implementing our own Design System.

Atomic Design is a methodology for creating design systems. There are five distinct levels in atomic design:

* Atoms
* Molecules
* Organisms
* Templates (or objects)
* Pages

CSS partials bundle, gzip and serve in 3 main files (our codebase version names come from the Spaceballs Movie, previous version was named Helmet, current one is the Schwartz):

* schwartz.css
* schwartz_medium.css
* schwartz_large.css

To reduce load, we have some more bundles for logged-in users, and other more autonomous (both visually and functionally) sections.

Written this way, CSS is now mobile first and based on Atomic architecture. This results in:

* Better rendering and painting performance, especially for mobiles
* Easier and faster feature development
* Consistent, better user experience
* Structural, easier to maintain, edit & extend CSS

## File Organization

> Don't agonize. Organize.<br/>
> -- Florynce Kennedy

In general, Skroutz CSS file organization follow somehow atomic design principles:

    stylesheets
    |
    ├── atoms
    │   ├── cards
    │   │   ├── cards.scss
    │   │   ├── ...
    │   ├── forms
    │   │   ├── forms.scss
    │   │   ├── ...
    │   ├── global
    │   │   ├── fonts.scss
    │   │   ├── ...
    │   ├── texts
    │   │   ├── headings.scss
    │   │   ├── ...
    │   ├── buttons
    │   │   ├── buttons.scss
    │   │   ├── ...
    │   ├── icons
    │   │   ├── icons.scss
    │   │   ├── ...
    │   └── ...
    │
    ├── helpers
    │   ├── animations.scss
    │   ├── functions.scss
    │   ├── variables.scss
    │   ├── states.scss
    │   └── ...
    │
    ├── layouts
    │   ├── layouts.scss
    │   └── ...
    │
    ├── molecules
    │   ├── rating.scss
    │   ├── pagination.scss
    │   └── ...
    │
    ├── organism
    │   ├── filters.scss
    │   ├── footer.scss
    │   └── ...
    │
    ├── pages
    │   ├── account
    │   │   ├── account_favorites.scss
    │   │   └── ...
    │   ├── cart
    │   │   ├── cart.scss
    │   │   └── ...


We split module CSS files into partials, for supporting responsive styles (such as **medium** and **large** viewports), cross browsing styles (such as **IE9** and **IE8**) and **localization** (L10n) adjustments as well. Not every module or partial has a respective responsive partial or localized version.

## Tools

#### Generate icon fonts
We use font icons. In order to add a new icon we use a [gulp task](https://github.com/nfroidure/gulp-iconfont) and the process is the following:

1) Add a new `.svg` icon into `app/assets/icons/` with a descriptive file name
2) Run `yarn icons`

The task will generate the glyphicon and add the code needed in `schwartz/helpers/_icons.scss`. The name of the new `.svg` file, will be prefixed with `icon-` and added in the `$icons` list.

Always test after. A `git diff` will show the new addition and only that. Nothing else should change. If that's the case, something went wrong.

Similarly, if you want to remove an icon, you only have to delete the file from `app/assets/icons/` and run `yarn icons`

## Misc

Be consistent.

If you’re editing code, take a few minutes to look at the code around you and determine its style. If they use spaces around all their arithmetic operators, you should too. If their comments have little boxes of hash marks around them, make your comments have little boxes of hash marks around them too.

The point of having style guidelines is to have a common vocabulary of coding so people can concentrate on what you’re saying rather than on how you’re saying it. We present global style rules here so people know the vocabulary, but local style is also important. If code you add to a file looks drastically different from the existing code around it, it throws readers out of their rhythm when they go to read it. Avoid this.

## Above all else

These are some basic techniques that we currently use for our stylesheets internally at [Skroutz](http://www.skroutz.gr). Feel free to take inspiration from them.
