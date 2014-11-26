# CSS Architecture and Coding Style Conventions

> When it comes to web development, <br/>
> the answer to most questions is “it depends”.<br/>
> -- Snooca

Welcome to [Skroutz](http://www.skroutz.gr) CSS Styleguide. Here are some techniques that help us in keeping CSS organised and structured, leading to code that is both easier to read and maintain.

This is the guide we use internally, for our own apps, at [Skroutz](http://www.skroutz.gr).

This guide contains a mashup of ideas from the [Github CSS styleguide](https://github.com/styleguide/css) and the [Scalable and Modular Architecture for CSS guide](http://smacss.com/) (SMACSS for short), with some sprinkle from the [Google CSS styleguide](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml).

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
* [File Organization](#file-organization)
* [Framework](#framework)
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


Here is a good example syntax:

```SCSS
/* This is a good CSS example! */

.foo {
  border: 1px solid #0f0;
  color: #000;
  background: rgba(0, 0, 0, .5);
}
```

```SCSS
// This is a good SCSS example!

.foo {
  @include transform(rotate(90deg));
  @include transition(transform .5s linear);
  color: $orange;
  font-size: 13px;

  &:hover {
    color: $red;
  }
}
```

### Comments

Try not to use CSS comments, instead use SASS comments for your comment block. We separate comments into 3 categories: descriptions, block titles and hints.

#### Description comments

Description comments are usually  needed to provide more informations about a css block. So description comments should always appear in a new line above the code block, and should always follow by a new line as well.

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

* Define color variables in `framework/_colors.scss`.
* Use the color variables and don't use hardcoded hex triplets directly in CSS files.
* Define font variables in `framework/_fonts.scss`. Currently, we mainly use `Verdana`, but you can also use `Georgia` and `Arial`.
* Try to use semantic names for your variables (Good examples: `$black: #000;`, `$arial: Arial, Helvetica, sans-serif;`).
* Try to use colors that we already use and if you want to use one that we don’t, you can add it in variables as long as we will need it again.
* If there is a variable that you want to use in a specific file and we don’t use it in general, define it at the top of the file (ex. `$facebook: #4363ac;` in `framework/_buttons.scss`)

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
  * **Bad** candidates for ids: navigation, item listings, item view pages (ex: issue view).

When styling a component, prefer class names over ids, prefer direct descendant selectors by default, and use as little specificity as possible. Here is a good example:

```SCSS
.category-list {

  & > li {
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

The intent is to keep the styles that pertain to a specific layout or module in a logically grouped way. We declare 2 redlines for three versions in our websites, 640px and 960px:

* Viewport < 640px (Mobile version).
* 641px < Viewport < 960px (Tablet version).
* 961px < Viewport (Desktop version).

Media queries suffixes we use, are the following:

* _tablet.scss (Tablet version).
* _mobile.scss (Mobile version).

We also use media queries to distinguish between [HiDPI displays](http://en.wikipedia.org/wiki/Retina_Display) (aka. Retina displays) and normal displays:

* _retina.scss (Retina version).

## Declaration Order

The most popular way to order CSS properties is grouped by type. Our preference is for structurally important properties (e.g. positioning and box-model) to be declared prior to all others.

## File Organization

> Don't agonize. Organize.<br/>
> -- Florynce Kennedy

In general, Skroutz CSS file organization should follow something like this:

    stylesheets
    |
    ├── framework
    │   ├── colors.scss
    │   ├── buttons.scss
    │   └── ...
    ├── partials
    │   ├── header.scss
    │   ├── header_tablet.scss
    │   ├── header_mobile.scss
    │   ├── header_retina.scss
    │   ├── header_ie9.scss
    │   ├── footer.scss
    │   ├── ...
    │   ├── el
    │   │   ├── header.scss
    │   │   ├── header_tablet.scss
    │   │   └── ...
    │   ├── tr
    │   │   ├── header.scss
    │   │   ├── header_tablet.scss
    │   │   └── ...

We split module CSS files into partials, for supporting responsive styles (such as **tablet** and **mobile** viewports), cross browsing styles (such as **IE9** and **IE8**) and **localization** (L10n) adjustments as well. Not every module or partial has a respective responsive partial or localized version.

## Framework

Skroutz Framework consists of the following reusable css partials.

* Functions
* Colors
* Fonts
* Buttons
* State
* Forms
* Grid
* Animations
* Modules

## Misc

Be consistent.

If you’re editing code, take a few minutes to look at the code around you and determine its style. If they use spaces around all their arithmetic operators, you should too. If their comments have little boxes of hash marks around them, make your comments have little boxes of hash marks around them too.

The point of having style guidelines is to have a common vocabulary of coding so people can concentrate on what you’re saying rather than on how you’re saying it. We present global style rules here so people know the vocabulary, but local style is also important. If code you add to a file looks drastically different from the existing code around it, it throws readers out of their rhythm when they go to read it. Avoid this.

## Above all else

These are some basic techniques that we currently use for our stylesheets internally at [Skroutz](http://www.skroutz.gr). Feel free to take inspiration from them.
