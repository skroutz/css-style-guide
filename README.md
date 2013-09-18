# CSS Architecture and Coding Style Conventions

> When it comes to web development, <br/>
> the answer to most questions is “it depends”.<br/>
> -- Snooca

Definitely, there is no "one true way" for developing large-scale websites. After having built several large-scale websites, we have discovered some techniques that help in keeping CSS organized and structured, leading to code that is both easier to read and maintain.

This is an attempt to document a consistent approach to site development when using CSS. Feel free to take this in its entirety or use only the parts that work best for you and your team.

This is the guide we use internally, for our own apps, at [Skroutz](http://www.skroutz.gr).

It contains a mashup of ideas from the [Github CSS styleguide](https://github.com/styleguide/css) and the [Scalable and Modular Architecture for CSS guide](http://smacss.com/) (SMACSS for short), with some sprinkle from the [Google CSS styleguide](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml).

## Table of Contents

* [Coding Style](#coding-style)
  * [Pixels vs. Ems](#pixels-vs-ems)
  * [Variables](#variables)
* [Specificity](#specificity)
  * [CSS Specificity guidelines](#css-specificity-guidelines)
* [File Organization](#file-organization)
  * [Base Rules](#base-rules)
  * [Layout Rules](#layout-rules)
  * [Module Rules](#module-rules)
* [Media Queries](#media-queries)
* [Declaration Order](#declaration-order)
  * [Sublime Instructions](#sublime-instructions)
* [Misc](#misc)

## Coding Style

* Use soft-tabs with a two space indent.
* Put spaces after `:` in property declarations.
* Put spaces before `{` in rule declarations.
* Use a semicolon `;` after every declaration. Even the last one.
* Use hex color codes `#000` unless using rgba.
* Use `//` for comment blocks (instead of `/* */`).
* Use shorthand properties where possible.
* Seperate rules by new lines.

Here is a good example syntax:

```CSS
// This is a good example!
.styleguide-format {
  border: 1px solid #0f0
  color: #000;
  background: rgba(0,0,0,0.5);
}
```

### Pixels vs. Ems

Use `px` for `font-size`, because it offers absolute control over text. Additionally, unit-less `line-height` is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the `font-size`.

### Variables

 * Define color variables in `_general.scss`.
 * Use the color variables and don't use hardcoded hex triplets directly in CSS files.
 * Colors should follow the [Color Naming Scheme](http://en.wikipedia.org/wiki/Color_Naming_System) notation.
 * Consult the [BNF syntax](http://people.csail.mit.edu/jaffer/Color/CNS-syntax) and [see the explanation](http://www.xanthir.com/blog/b4JS10).
 * Define descriptive colors and then [define functional areas](http://sachagreif.com/sass-color-variables/) that use them.

Here is a good example of variable usage:

```SCSS
// This is a good example of variable usage!
$grey: #707070;
$dark-grey: #666666;

$text-color: $grey;
$link-color: $grey;
$border-color: $dark-grey;

.class {
  color: $text-color;
  border-color: $border-color;
}

a {
  color: $link-color;
}
```

## Specificity

> CSS takes into account both code order and selector specificity.<br/>
> So, don't fight the css cascade.

Elements that occur **exactly once** inside a page should use IDs, otherwise, use classes. When in doubt, use a class name.

  * **Good** candidates for ids: header, footer, modal popups.
  * **Bad** candidates for ids: navigation, item listings, item view pages (ex: issue view).

When styling a component, start with an element + class namespace (prefer class names over ids), prefer direct descendant selectors by default, and use as little specificity as possible. Here is a good example:

```HTML
<!-- This is a good example -->
<ul class="category-list">
  <li class="item">Category 1</li>
  <li class="item">Category 2</li>
  <li class="item">Category 3</li>
</ul>
```

```SCSS
ul.category-list { // element + class namespace

  & > li { // direct descendant selector > for list items
    list-style-type: disc;
  }

  a { // minimal specificity for all links
    color: #ff0000;
  }
}
```

### CSS Specificity guidelines

* If you must use an id selector (`#selector`) make sure that you have no more than _one_ in your rule declaration. A rule like `#header .search #quicksearch { ... }` is considered harmful.
* When modifying an existing element for a specific use, try to use specific class names. Instead of `.listings-layout.bigger` use rules like `.listings-layout.listings-bigger`. Think about `ack/grep`ing your code in the future.
* The class names `disabled`, `mousedown`, `danger`, `hover`, `selected`, and `active` should _always_ be namespaced by a class (`button.classy.selected` is a good example).

## File Organization

> Don't agonize. Organize.<br/>
> -- Florynce Kennedy

At the very core of CSS Architecture is categorization. We have three types of categories:

* [Base Rules](#base-rules)
* [Layouts Rules](#layouts-rules)
* [Module Rules](#module-rules)

Each category has certain guidelines that apply to it. This somewhat succinct separation allows us to ask ourselves questions during the development process. _How_ are we going to code things and _why_ are we going to code them that way?

Much of the purpose of categorizing things is to codify patterns—things that repeat themselves within our design. Repeating the same process results in less code, easier maintenance, and greater consistency in the user experience. These are all wins. Exceptions to the rule can be advantageous but they should be justified.

* Base rules are the **defaults**. They are almost exclusively single element selectors but could include attribute selectors, pseudo-class selectors, child selectors or sibling selectors.

* Layout rules divide the page into sections. Layouts hold one or more modules together.

* Modules are the reusable, modular parts of our design. They are the callouts, the sidebar sections, the product lists and so on.

In general, the CSS file organization should follow something like this:

    styles
    ├── base
    │   ├── reset.scss
    │   ├── functions.scss
    │   └── general.scss
    ├── layouts
    │   ├── layout1.scss
    │   ├── layout2.scss
    │   └── ...
    └── modules
        ├── header.scss
        ├── footer.scss
        ├── module.scss
        ├── module.scss
        └── ...

Furthermore, we split module CSS files into partials, for supporting responsive styles (such as **tablet** and **mobile** viewports), cross browsing styles (such as **IE9** and **IE8**) and **localization** (L10n) adjustments as well. Not every module or partial has a respective responsive partial or localized version.

Organization for layout and module should follow something like this:

    styles
    ├── layouts
    │   ├── layout1.scss
    │   ├── layout2.scss
    │   ├── ...
    │   ├── el
    │   │   ├── layout1.scss
    │   │   └── ...
    │   ├── tr
    │   │   ├── layout1.scss
    │   │   └── ...
    │   └── ...
    ├── partials
    │   ├── header.scss
    │   ├── header_tablet.scss
    │   ├── header_mobile.scss
    │   ├── header_retina.scss
    │   ├── header_ie9.scss
    │   ├── header_ie8.scss
    │   ├── footer.scss
    │   ├── module.scss
    │   ├── el
    │   │   ├── header.scss
    │   │   ├── header_tablet.scss
    │   │   └── ...
    │   ├── tr
    │   │   ├── header.scss
    │   │   ├── header_tablet.scss
    │   │   └── ...
    │   └── ...
    ├── modules
    |   └── ...
    └── ...

### Base Rules

A Base rule is applied to an element using an element selector, a descendant selector, or a child selector, along with any pseudo-classes. Base rules **shouldn't include any class or ID selectors**. They define the default styling for how that element should look in all occurrences on the page.

Base styles include setting resets, heading sizes, default link styles, default font styles, and body backgrounds. There should be no need to use `!important` in a Base style.

### Layouts Rules

Layout styles dictate the major and minor components of a page. They can also be divided into major and minor styles based on reuse. Major layout styles such as headers and footers are traditionally styled using ID selectors. Generally, a Layout style **has only a single selector**: a single ID or class name.

#### Using ID selectors

Using ID attributes in our HTML can be a good thing and in some cases, absolutely necessary. For example, they provide efficient hooks for JavaScript. For CSS, however, ID selectors aren’t necessary as the performance difference between ID and class selectors is nearly non-existent and can make styling more complicated due to increasing specificity.

### Module Rules

A Module is a more discrete component of the page. Modules sit inside Layout components or can sometimes sit within other Modules, too. Each Module should be designed to exist as a standalone component. In doing so, the page will be more flexible. If done right, Modules can easily be moved to different parts of the layout without breaking.

When defining the rule set for a module, **avoid using IDs and element selectors**, sticking **only to class names**. A module will likely contain a number of elements and there is likely to be a desire to use descendent or child selectors to target those elements.

#### Avoid element selectors

Use child or descendant selectors with element selectors if the element selectors will and can be predictable. Using `.class` span is great if a span will predictably be used and styled the same way every time while within that module. The problem is that as a project grows in complexity, the more likely that you will need to expand a component’s functionality and the more limited you will be, having used such a generic element within your rule.

If you do wish to use an element selector, it should be within one level of a class selector. In other words, you should be in a situation to use child selectors.

#### Only include a selector that includes semantics

A span or div holds none. A heading has some. A class defined on an element has plenty. By adding the classes to the elements, we have increased the semantics of what those elements mean and removed any ambiguity when it comes to styling them.

If you do wish to use an element selector, it should be within one level of a class selector. In other words, you should be in a situation to use child selectors.

#### Subclassing Modules

When we have the same module in different sections, the first instinct is to use a parent element to style that module differently. The problem with this approach is that you can run into specificity issues that require adding even more selectors to battle against it or to quickly fall back to using !important.

With sub-classing the module, both the base module and the sub-module class names get applied to the HTML element. Try to avoid conditional styling based on location. If you are changing the look of a module for usage elsewhere on the page or site, sub-class the module instead.

While more specific layout components assigned with IDs could be used to provide specialized styling for modules, sub-classing the module will allow the module to be moved to other sections of the site more easily and you will avoid increasing the specificity unnecessarily.

#### Using !important

Be cautious. Leave !important off until you **actually** and **truly** need it.

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

We use [CSSComb](http://csscomb.com) to order our styles. Please install the appropriate version for your editor.

Our order preference is defined in the [csscomb-default-style.json](/skroutz/css-style-guide/blob/master/csscomb/csscomb-default-style.json) JSON file. You have to configure your editor to use this style (by passing the `-s` option).

### Sublime Instructions

 * Install the csscomb package
   * Open `Preferences`, `Package Control` then choose `Install Package`
   * Select `CSScomb`
 * Go to the CSScomb package folder
   * Linux: `~/.config/sublime-text-2/Packages/CSScomb/csscomb/libs`
 * Download the [csscomb-default-style.json](/csscomb/csscomb-default-style.json) in this folder
   * `$ wget https://raw.github.com/skroutz/css-style-guide/master/csscomb/csscomb-default-style.json`
 * Edit `call_script.php` and make it load the default style JSON
   * `$ vim call_script.php`
 * Change the 3rd argument of the $csscomb->csscomb() call in order to load and use the default style, i.e. change
   `$csscomb->csscomb($argv[1], false, null);`
   to
   `$csscomb->csscomb($argv[1], false, file_get_contents('csscomb-default-style.json', true));`

## Misc

Be consistent.

If you’re editing code, take a few minutes to look at the code around you and determine its style. If they use spaces around all their arithmetic operators, you should too. If their comments have little boxes of hash marks around them, make your comments have little boxes of hash marks around them too.

The point of having style guidelines is to have a common vocabulary of coding so people can concentrate on what you’re saying rather than on how you’re saying it. We present global style rules here so people know the vocabulary, but local style is also important. If code you add to a file looks drastically different from the existing code around it, it throws readers out of their rhythm when they go to read it. Avoid this.

## Above all else

Follow your :heart:
