# CSS Architecture and Coding Style Conventions

> When it comes to web development, <br/>
> the answer to most questions is “it depends”.<br/>
> -- Snooca

Definitely, there is not "one true way" of developing right large-scale websites. After having build websites on a large scale, we have discover some techniques that can keep CSS organized and structured, leading to code that is easier to build and maintain.

This is an attempt to document a consistent approach to site development when using CSS. Feel free to take this in its entirety or use only the parts that work best for you. Or don’t use it at all. 


## Table of Contents

* [Coding Style](#coding-style)
* [File Organization](#file-organization)
* [Media Queries](#media-queries)
* [Specificity](#specificity)
* [Declaration Order](#declaration-order)
* [Misc](#misc)


## Coding Style

* Use soft-tabs with a two space indent.
* Put spaces after : in property declarations.
* Put spaces before { in rule declarations.
* Add ; in last rule.
* Use hex color codes #000 unless using rgba.
* Use // for comment blocks (instead of /* */).


## File Organization

> Don't agonize. Organize.<br/>
> -- Florynce Kennedy 

At the very core of CSS Architecture is categorization. At Skroutz.gr, there are three types of categories:

* [Base Rules](#base-rules)
* [Layouts Rules](#layouts-rules)
* [Module Rules](#module-rules)

Each category has certain guidelines that apply to it. This somewhat succinct separation allows us to ask ourselves questions during the development process. How are we going to code things and why are we going to code them that way?

Much of the purpose of categorizing things is to codify patterns—things that repeat themselves within our design. Repetition results in less code, easier maintenance, and greater consistency in the user experience. These are all wins. Exceptions to the rule can be advantageous but they should be justified.

Base rules are the defaults. They are almost exclusively single element selectors but it could include attribute selectors, pseudo-class selectors, child selectors or sibling selectors.

Layout rules divide the page into sections. Layouts hold one or more modules together.

Modules are the reusable, modular parts of our design. They are the callouts, the sidebar sections, the product lists and so on.

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


### Base Rules

A Base rule is applied to an element using an element selector, a descendent selector, or a child selector, along with any pseudo-classes. It doesn’t include any class or ID selectors. It is defining the default styling for how that element should look in all occurrences on the page.

Base styles include setting resets, heading sizes, default link styles, default font styles, and body backgrounds. There should be no need to use!important in a Base style.


### Layouts Rules

Layout styles dictating the major and minor components of a page. They can also be divided into major and minor styles based on reuse. Major layout styles such as header and footer are traditionally styled using ID selectors. Generally, a Layout style only has a single selector: a single ID or class name. 


### Module Rules

A Module is a more discrete component of the page. Modules sit inside Layout components or can sometimes sit within other Modules, too. Each Module should be designed to exist as a standalone component. In doing so, the page will be more flexible. If done right, Modules can easily be moved to different parts of the layout without breaking.

When defining the rule set for a module, avoid using IDs and element selectors, sticking only to class names. A module will likely contain a number of elements and there is likely to be a desire to use descendent or child selectors to target those elements.


## Media Queries

> The point is that you want to have a system that is responsive.<br/>
> -- Bill Joy 

Media queries are an approach to managing state change. A media query, depending on the file size, can be defined as a separate partial file, or it can be defined within @media block within a specific style sheet (at the bottom).

The intent is to keep the styles that pertain to a specific layout or module with the rest of these. We declare 2 redlines for three versions of our site, 640px and 960px:

* Viewport < 640px (Mobile version).
* 641px < Viewport < 960px (Tablet version).
* 961px < Viewport (Desktop version).


## Specificity

> CSS takes into account both code order and selector specificity.<br/> 
> So, don't fight the css cascade.

Elements that occur exactly once inside a page should use IDs, otherwise, use classes. When in doubt, use a class name.

  * Good candidates for ids: header, footer, modal popups.
  * Bad candidates for ids: navigation, item listings, item view pages (ex: issue view).

When styling a component, start with an element + class namespace (prefer class names over ids), prefer direct descendant selectors by default, and use as little specificity as possible. Here is a good example:
    
    <ul class="category-list">
      <li class="item">Category 1</li>
      <li class="item">Category 2</li>
      <li class="item">Category 3</li>
    </ul>


### CSS Specificity guidelines

* If you must use an id selector (#selector) make sure that you have no more than one in your rule declaration. A rule like #header .search #quicksearch { ... } is considered harmful.
* When modifying an existing element for a specific use, try to use specific class names. Instead of .listings-layout.bigger use rules like .listings-layout.listings-bigger. Think about ack/greping your code in the future.
* The class names disabled, mousedown, danger, hover, selected, and active should always be namespaced by a class (button.classy.selected is a good example).


### ID attributes

Using ID attributes in our HTML can be a good thing and in some cases, absolutely necessary. For example, they provide efficient hooks for JavaScript. For CSS, however, ID selectors aren’t necessary as the performance difference between ID and class selectors is nearly non-existent and can make styling more complicated due to increasing specificity.


### Avoid element selectors

Use child or descendant selectors with element selectors if the element selectors will and can be predictable. Using .class span is great if a span will predictably be used and styled the same way every time while within that module. The problem is that as a project grows in complexity, the more likely that you will need to expand a component’s functionality and the more limited you will be in having used such a generic element within your rule.

If you do wish to use an element selector, it should be within one level of a class selector. In other words, you should be in a situation to use child selectors.


### Only include a selector that includes semantics

A span or div holds none. A heading has some. A class defined on an element has plenty. By adding the classes to the elements, we have increased the semantics of what those elements mean and removed any ambiguity when it comes to styling them.


### Subclassing Modules

When we have the same module in different sections, the first instinct is to use a parent element to style that module differently. The problem with this approach is that you can run into specificity issues that require adding even more selectors to battle against it or to quickly fall back to using !important.

With sub-classing the module, both the base module and the sub-module class names get applied to the HTML element. Try to avoid conditional styling based on location. If you are changing the look of a module for usage elsewhere on the page or site, sub-class the module instead.

While more specific layout components assigned with IDs could be used to provide specialized styling for modules, sub-classing the module will allow the module to be moved to other sections of the site more easily and you will avoid increasing the specificity unnecessarily.


### Using !important

We have to be cautious. Leave !important off until you actually and truly need it. 


## Declaration Order

The most popular way to order CSS properties is grouped by type. Our preference is for structurally important properties (e.g. positioning and box-model) to be declared prior to all others. This is what we use:

    position
    top
    right
    bottom
    left
    z-index
    display
    visibility
    float
    clear
    overflow
    -ms-overflow-x
    -ms-overflow-y
    overflow-x
    overflow-y
    width
    height
    min-width
    min-height
    max-width
    max-height
    -webkit-box-sizing
    -moz-box-sizing
    box-sizing
    clip
    margin
    margin-top
    margin-right
    margin-bottom
    margin-left
    padding
    padding-top
    padding-right
    padding-bottom
    padding-left
    border
    border-spacing
    border-collapse
    border-width
    border-style
    border-color
    border-top
    border-top-width
    border-top-style
    border-top-color
    border-right
    border-right-width
    border-right-style
    border-right-color
    border-bottom
    border-bottom-width
    border-bottom-style
    border-bottom-color
    border-left
    border-left-width
    border-left-style
    border-left-color
    -webkit-border-radius
    -moz-border-radius
    border-radius
    -webkit-border-top-right-radius
    -moz-border-top-right-radius
    border-top-right-radius
    -webkit-border-bottom-right-radius
    -moz-border-bottom-right-radius
    border-bottom-right-radius
    -webkit-border-bottom-left-radius
    -moz-border-bottom-left-radius
    border-bottom-left-radius
    -webkit-border-top-left-radius
    -moz-border-top-left-radius
    border-top-left-radius
    -webkit-border-image
    -moz-border-image
    -o-border-image
    border-image
    -webkit-border-image-source
    -moz-border-image-source
    -o-border-image-source
    border-image-source
    -webkit-border-image-slice
    -moz-border-image-slice
    -o-border-image-slice
    border-image-slice
    -webkit-border-image-width
    -moz-border-image-width
    -o-border-image-width
    border-image-width
    -webkit-border-image-outset
    -moz-border-image-outset
    -o-border-image-outset
    border-image-outset
    -webkit-border-image-repeat
    -moz-border-image-repeat
    -o-border-image-repeat
    border-image-repeat
    -webkit-border-top-image
    -moz-border-top-image
    -o-border-top-image
    border-top-image
    -webkit-border-right-image
    -moz-border-right-image
    -o-border-right-image
    border-right-image
    -webkit-border-bottom-image
    -moz-border-bottom-image
    -o-border-bottom-image
    border-bottom-image
    -webkit-border-left-image
    -moz-border-left-image
    -o-border-left-image
    border-left-image
    -webkit-border-corner-image
    -moz-border-corner-image
    -o-border-corner-image
    border-corner-image
    -webkit-border-top-left-image
    -moz-border-top-left-image
    -o-border-top-left-image
    border-top-left-image
    -webkit-border-top-right-image
    -moz-border-top-right-image
    -o-border-top-right-image
    border-top-right-image
    -webkit-border-bottom-right-image
    -moz-border-bottom-right-image
    -o-border-bottom-right-image
    border-bottom-right-image
    -webkit-border-bottom-left-image
    -moz-border-bottom-left-image
    -o-border-bottom-left-image
    border-bottom-left-image
    -webkit-box-shadow
    -moz-box-shadow
    box-shadow
    outline
    outline-width
    outline-style
    outline-color
    outline-offset
    opacity
    -ms-filter:'progid:DXImageTransform.Microsoft.Alpha
    filter:progid:DXImageTransform.Microsoft.Alpha(Opacity
    -webkit-flex-direction
    -moz-flex-direction
    -ms-flex-direction
    -o-flex-direction
    flex-direction
    -webkit-flex-order
    -moz-flex-order
    -ms-flex-order
    -o-flex-order
    flex-order
    -webkit-flex-pack
    -moz-flex-pack
    -ms-flex-pack
    -o-flex-pack
    flex-pack
    -webkit-flex-align
    -moz-flex-align
    -ms-flex-align
    -o-flex-align
    flex-align
    background
    filter:progid:DXImageTransform.Microsoft.AlphaImageLoader
    background-color
    background-image
    background-attachment
    background-position
    -ms-background-position-x
    -ms-background-position-y
    background-position-x
    background-position-y
    background-clip
    background-origin
    background-size
    background-repeat
    box-decoration-break
    font
    font-family
    font-size
    font-weight
    font-style
    font-variant
    font-size-adjust
    font-stretch
    color
    text-align
    -ms-text-align-last
    text-align-last
    vertical-align
    text-indent
    text-decoration
    text-emphasis
    text-emphasis-position
    text-emphasis-style
    text-emphasis-color
    -ms-text-justify
    text-justify
    text-outline
    text-transform
    text-wrap
    -ms-text-overflow
    text-overflow
    text-overflow-ellipsis
    text-overflow-mode
    text-shadow
    white-space
    word-spacing
    -ms-word-wrap
    word-wrap
    -ms-word-break
    word-break
    -moz-tab-size
    -o-tab-size
    tab-size
    -webkit-hyphens
    -moz-hyphens
    hyphens
    letter-spacing
    table-layout
    caption-side
    empty-cells
    list-style
    list-style-position
    list-style-type
    list-style-image
    quotes
    content
    counter-increment
    counter-reset
    -ms-writing-mode
    src
    line-height
    -ms-interpolation-mode
    -webkit-filter
    -ms-filter
    filter
    resize
    cursor
    nav-index
    nav-up
    nav-right
    nav-down
    nav-left
    -webkit-transition
    -moz-transition
    -ms-transition
    -o-transition
    transition
    -webkit-transition-delay
    -moz-transition-delay
    -ms-transition-delay
    -o-transition-delay
    transition-delay
    -webkit-transition-timing-function
    -moz-transition-timing-function
    -ms-transition-timing-function
    -o-transition-timing-function
    transition-timing-function
    -webkit-transition-duration
    -moz-transition-duration
    -ms-transition-duration
    -o-transition-duration
    transition-duration
    -webkit-transition-property
    -moz-transition-property
    -ms-transition-property
    -o-transition-property
    transition-property
    -webkit-transform
    -moz-transform
    -ms-transform
    -o-transform
    transform
    -webkit-transform-origin
    -moz-transform-origin
    -ms-transform-origin
    -o-transform-origin
    transform-origin
    -webkit-animation
    -moz-animation
    -ms-animation
    -o-animation
    animation
    -webkit-animation-name
    -moz-animation-name
    -ms-animation-name
    -o-animation-name
    animation-name
    -webkit-animation-duration
    -moz-animation-duration
    -ms-animation-duration
    -o-animation-duration
    animation-duration
    -webkit-animation-play-state
    -moz-animation-play-state
    -ms-animation-play-state
    -o-animation-play-state
    animation-play-state
    -webkit-animation-timing-function
    -moz-animation-timing-function
    -ms-animation-timing-function
    -o-animation-timing-function
    animation-timing-function
    -webkit-animation-delay
    -moz-animation-delay
    -ms-animation-delay
    -o-animation-delay
    animation-delay
    -webkit-animation-iteration-count
    -moz-animation-iteration-count
    -ms-animation-iteration-count
    -o-animation-iteration-count
    animation-iteration-count
    -webkit-animation-direction
    -moz-animation-direction
    -ms-animation-direction
    -o-animation-direction
    animation-direction
    unicode-bidi
    direction
    -webkit-columns
    -moz-columns
    columns
    -webkit-column-span
    -moz-column-span
    column-span
    -webkit-column-width
    -moz-column-width
    column-width
    -webkit-column-count
    -moz-column-count
    column-count
    -webkit-column-fill
    -moz-column-fill
    column-fill
    -webkit-column-gap
    -moz-column-gap
    column-gap
    -webkit-column-rule
    -moz-column-rule
    column-rule
    -webkit-column-rule-width
    -moz-column-rule-width
    column-rule-width
    -webkit-column-rule-style
    -moz-column-rule-style
    column-rule-style
    -webkit-column-rule-color
    -moz-column-rule-color
    column-rule-color
    break-before
    break-inside
    break-after
    page-break-before
    page-break-inside
    page-break-after
    orphans
    widows
    -ms-zoom
    zoom
    max-zoom
    min-zoom
    user-zoom
    orientation


## Misc


