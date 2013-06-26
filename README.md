# LESS Style Guide

This document outlines the basic CSS/LESS conventions we use at WebMD HS. This is an open document and everything is up for discussion/consideration. We have a tremendous amount of legacy code that does not adhere to these rules, and if you find yourself in one of those files it is in everyone's best interest to refactor it.

## Contents

1. [Structure](#structure)
    * [Organization](#organization)
    * [Pattern Libary](#pattern-library)
    * [Themes](#themes)
* [Style Guide](#style-guide)
    * [Indentation](#indentation)
    * [Selectors](#selectors)
    * [Properties](#properties)
    * [Variables](#variables)
    * [Mixins](#mixins)
* [Stay Rad](#stay-rad)

## Structure

### Organization

You should include exactly __one__ stylesheet on your page and no more. This base file should then import all dependancies and components. Components and styles for different sections should be contained in their own stylesheet and should not cross-pollinate. The home page styles directory, for example, contains the files `homepage.less` `layout.less` `featuredNews.less` `featuredVideo.less` `promoWeblets.less`

`homepage.less` is included on the page and imports the different files for each section like this:

```css
@import '/themes/common/css/minimal';
@import 'layout';
@import 'featuredNews';
@import 'featuredVideo';
@import 'promoWeblets';
```

And that's it. `minimal.less` and the legacy-filled `theme_builder.less` import site chrome like the header, footer, nav, and other junk you don't have to worry about unless you're Obelisk. *One of these files must be included.* The master `themes_common.less` and a sponsor's `theme.less` are magically included in every stylesheet by default and import our [Pattern Library](#pattern-library) components, so you can utilize our global variables and mixins in any LESS file on the site.

### Pattern Library

Our [Pattern Library](http://localhost/wminternal/ui/patternlibrary) is broken up into three pieces, `core`, `components`, and `modules`.

__*Core*__ contains base styles for our site's body, header, footer, and navigation. It also handles a lot of the global responsive bits. Anything between the nav and the footer should not use anything from core.

__*Components*__ describe define things like forms, buttons, tables, sprites, grids, etc, that are used frequently across the site. All of these components are organized into mixins and included for you so you can call them at any time, but some have special instructions. Check the documentation.

__*Modules*__ are more specialized components like modals, tab panels, and progress bars. Since these components are used infrequently and tend to be more customizable they live here.

There is one more piece of the Pattern Library that contains legacy/deprectaed components, and you should stay away from it unless you're Obelisk.

### Theme Variables

Use common variables for things like colors and gutters. This way clients can override variables with their branding colors and fonts if need be. All global variables can all be located in `themes_common.less`, but the ones we recommend using are in the Pattern Library.

Here's a good example of how to use theme variables wisely.

```css
.class {
    color: @themePrimary;
    background: @matteShading;
    border: 1px solid @borderShade;
}
```

---

## Style Guide

We want to make sure our LESS looks familiar to everyone editing it. That's why these guidelines are not simply suggestions, but the *de facto* style you should be coding. The Front-End Community of Practice decides on the style based on our standards. Don't get busted by the COPs.

### Indentation

Indent four spaces, no tabs

### Selectors

Class and ID names should be `camelCase`

```css
.className {...}
#theIdName {...}
```

State rules added via Javascript should us hyphens between words

```css
.is-visible {...}
```

HTML elements should be lowercase, of course. That said, avoid styling naked tags.

```css
div, span {...}
```

Put a space between selector and opening bracket and put the closing bracket on a separate, non-indented line

```css
.class {
    color: #000;
}
```

Return after each closing bracket

```css
.class1 {
    color: #000;
}

.class1 {
    color: #000;
}
```

Put multiple selectors on separate lines, separated by commas

```css
.class1,
.class2,
.class3,
.class4 {
    color: #000;
}
```

### Properties

Make sure there is a space between property and value, each property is indented one level, and end each line with a semi-colon `;`

```css
div {
    color: #000;
    padding: 16px;
    height: 100px;
}
```

Specify units (`px, em, %`) unless it is `0`

```css
padding: 10px 0 15px;
```

Specify HEX values for colors

```
#000
#FF0138
```

Use LESS functions when adjust hue, saturation, lightness, or transparency

```css
color: fade(#000, 50%);
background: darken(#FFF, 50%);
```

Strings must use single-quotes

```css
a:after {
    content: 'pizza';
}
```

CSS3 properties that support layering should on separate lines, with the semi-colon on the last line

```css
.class {
    box-shadow:
        0 2px 4px #000,
        0 4px 8px #333;
    color: #000;
}
```

### Variables

Variables must be declared with `@`

```css
@themeColor: #F00;
```

Any color or layout value used more than once should be a variable

```css
@alertColor: #0F0;
@columnWidth: 80px;
```

__Any variable that is available for client customization *must be declared globally* in themes_common__

### Mixins

Always add parenthesis to declare mixins. Mixins should be styled like selectors. Do not put a space after the mixin name.

```css
.mixin() {
    color: #F00;
}
```

Include mixins first when declaring mixins inside selectors

```css
div {
    .mixin();
    padding: 16px;
}
```

Define mixins at the bottom of the document or in their own separate file via `@import`

```css
div {
    .mixin();
}
...
.mixin() {
    color: #F00;
}
```

Do not put spaces around parameters

```css
.mixin(@color) {
    background: @color;
}
```

Specify default parameters whenever possible.

```css
.mixin(@color: #F00) {
    background: @color;
}
```

When calling mixins, you do not need to specify the parameter name, just the value

```css
.class {
    .mixin(#F00, 100px);
}
```

Multiple parameters with values should be separated on their own lines.

```css
.mixin(
    @color: #F00,
    @width: 100px
    ) {
    background: @color;
    width: @width;
}
```

Guarded mixins with boolean values that are true do not need `= true`

```css
.mixin(@boolean) when (@boolean) {...}
```

Use the `when not` keyword when specifying falsey booleans

```css
.mixin(@boolean) when not (@boolean) {...}
```

Use bundles to contain similar mixins

```css
#gradient {
    .linear() {...}
    .radial() {...}
}
```

Call the bundles with the greater than `>` character

```css
div {
    #gradient > .linear(...);
}
```

__Make sure you're not redefining mixins that exist globally! Check components > common.less__

## Stay Rad

* Don't use inline styles
* Use semantically descriptive classnames (`.orangeButton` is terrible)
* Never nest deeper than three levels (The Inception Rule)
* Never nest an ID within and ID
* Avoid name spacing with element + class names unless it's a state rule (i.e., `div.class`)
* Don't repeat yourself (DRY), abstract patterns into reusable mixins and variables
* Use shorthand whenever possible (`margin` instead of `margin-top` + `margin-right`, etc.)
* group properties together (layout, fonts, background, prefixes, hacks)
* Use the star-hack `*property: value;` to easily target IE7
* When importing LESS files, do not put .less extension (`@import 'component'`)
* If your LESS file is over 100 lines it's time to start breaking things apart
* If you're doing something dumb, document why with a `//` comment
