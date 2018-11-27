# Novatopo CSS / Sass Styleguide

*A mostly reasonable approach to CSS and Sass. This styleguide was based on the Airbnb styleguide.*

## Table of Contents

1. [Terminology](#terminology)
    - [Rule Declaration](#rule-declaration)
    - [Selectors](#selectors)
    - [Properties](#properties)
1. [CSS](#css)
    - [Formatting](#formatting)
    - [Comments](#comments)
    - [Units](#units)
    - [OOCSS and BEM](#oocss-and-bem)
    - [ID Selectors](#id-selectors)
    - [JavaScript hooks](#javascript-hooks)
    - [Border](#border)
1. [Sass](#sass)
    - [Syntax](#syntax)
    - [Ordering](#ordering-of-property-declarations)
    - [Variables](#variables)
    - [Mixins](#mixins)
    - [Extend directive](#extend-directive)
    - [Nested selectors](#nested-selectors)
1. [Translation](#translation)
1. [License](#license)

## Terminology

### Rule declaration

A “rule declaration” is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:

```css
.listing {
  font-size: 18px;
  line-height: 1.2;
}
```

### Selectors

In a rule declaration, “selectors” are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:

```css
.my-element-class {
  /* ... */
}

[aria-hidden] {
  /* ... */
}
```

### Properties

Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:

```css
/* some selector */ {
  background: #f1f1f1;
  color: #333;
}
```

**[⬆ back to top](#table-of-contents)**

## CSS

### Formatting

* Use soft tabs (2 spaces) for indentation.
* Prefer dashes over camelCasing in class names.
  - Underscores and PascalCasing are okay when we use BEM (see [OOCSS and BEM](#oocss-and-bem) below).
* Do not use ID selectors.
* When using multiple selectors in a rule declaration, give each selector its own line.
* Put a space before the opening brace `{` in rule declarations.
* In properties, put a space after, but not before, the `:` character.
* Put closing braces `}` of rule declarations on a new line.
* Put blank lines between rule declarations.

**Bad**

```css
.avatar{
    border-radius:50%;
    border:2px solid white; }
.no, .nope, .not_good {
    // ...
}
#lol-no {
  // ...
}
```

**Good**

```css
.avatar {
  border-radius: 50%;
  border: 2px solid white;
}

.one,
.selector,
.per-line {
  // ...
}
```

### Comments

* Prefer line comments (`//` in Sass-land) to block comments.
* Prefer comments on their own line. Avoid end-of-line comments.
* Write detailed comments for code that isn't self-documenting:
  - Uses of z-index
  - Compatibility or browser-specific hacks

### Units

Recommendations of unit types per media type:

Media | Recommended | Occasional use | Infrequent use | Not recommended
:--- | :--- | :--- | :--- | :---
Screen | em, rem, % | px | ch, ex, vw, vh, vmin, vmax | cm, mm, in, pt, pc
Print | em, rem, % | cm, mm, in, pt, pc | ch, ex | px, vw, vh, vmin, vmax

#### Relative units
[Relative units](http://www.w3.org/TR/css3-values/#font-relative-lengths)
play nicely with both screen and print media types and should be
the most frequently used CSS units.

Unit | Description
:--- | :---
% | percentage, relative to the same property of the parent element
[em](http://www.w3.org/TR/css3-values/#em-unit) | relative to font size of the element
[rem](http://www.w3.org/TR/css3-values/#rem-unit) | relative to font size of the root element
[ch](http://www.w3.org/TR/css3-values/#ch-unit) | relative to width of the "0" (ZERO, U+0030) glyph in the element's font
[ex](http://www.w3.org/TR/css3-values/#ex-unit) | relative to x-height of the font

#### Absolute units
Physical units (e.g. cm, mm, in, pc, and pt)
should only be used for print style sheets,
while pixels (px) should only be used for the screen.
While there are consistent conversions among all of these
[absolute length units](http://www.w3.org/TR/css3-values/#absolute-lengths),
[depending on the device, CSS units can actually mean different things](http://omnicognate.wordpress.com/2013/01/07/in-css-px-is-not-an-angular-measurement-and-it-is-not-non-linear/).
For example, while `1cm` in CSS should print as one physical centimeter,
there's no guarantee that `1cm` in CSS results in one physical centimeter
on the screen.

Unit | Description | cm | mm | in | pc | pt | px
:--- | :--- | ---: | ---: | ---: | ---: | ---: | ---:
cm | centimeter | `1cm` | `10mm` | | | |
mm | millimeter | `1/10cm` | `1mm` | | | |
in | inch | `2.54cm` | `25.4mm` | `1in` | `6pc` | `72pt` | `96px`
pc | pica | | | `1/6in` | `1pc` | `12pt` | `16px`
pt | point | | | `1/72in` | `1/12pc` | `1pt` | `4/3px`
px | pixel | | | `1/96in` | `1/16pc` | `3/4pt` | `1px`

#### Viewport units

[Viewport-percentage length units](http://www.w3.org/TR/css3-values/#viewport-relative-lengths)
should be used with caution, as there is still some
[lingering bugs with their implementation](http://caniuse.com/#feat=viewport-units).

Unit | Description
:--- | :---
[vw](http://www.w3.org/TR/css3-values/#vw-unit) | relative to 1% of viewport's width
[vh](http://www.w3.org/TR/css3-values/#vh-unit) | relative to 1% of viewport's height
[vmin](http://www.w3.org/TR/css3-values/#vmin-unit) | relative to 1% of viewport's smaller dimension
[vmax](http://www.w3.org/TR/css3-values/#vmax-unit) | relative to 1% of viewport's larger dimension

#### Contexts

##### Document-level
Assume the root font size is `16px` but don't require it to be so. Either declare the font size as `100%` or don't declare the `font-size` property at all.

```css
html {
  font-size: 100%;
}
```

##### Borders
Most likely use `px`, as most of the time, the border shouldn't need to scale.
```css
.Component {
  border-bottom: 1px solid #ccc;
}
```

##### Font-size
Font-size should only be applied at the lowest possible child elements,
in order to minimize its cascading impact on relative units.
While both of the following two examples are essentially equivalent,
only the first is recommended.

**DO**:
```css
.Component {
}
.Component-heading {
  font-size: 1.2em;
}
.Component-body {
  font-size: 0.9em;
}
```
**DO NOT**:
```css
.Component {
  font-size: 1.2em;
}
.Component-heading {
  font-size: 1em;
}
.Component-body {
  font-size: 0.75em;
}
```

## Padding and margin
In order to ensure consistent use of whitespace throughout the application,
given a component could be used in a variety of contexts,
it may be best to use `rem` for margin and padding than `em`.

```css
.Component {
  margin: 1rem 0;
  padding: 1rem;
}
```

## Media queries
[Only use `em` within media query definitions, never pixels](http://blog.cloudfour.com/the-ems-have-it-proportional-media-queries-ftw/).
Until there's wider [`rem` support within media queries](http://fvsch.com/code/bugs/rem-mediaquery/),
[`rem` should be avoided in media queries as well](http://codeboxers.com/em-vs-px-vs-rem-in-media-queries/).

```css
@media (min-width: 20em) {
  .Component {
    background-color: blue;
  }
}
```

### OOCSS and BEM

Use combination of OOCSS and BEM for these reasons:

  * It helps create clear, strict relationships between CSS and HTML
  * It helps us create reusable, composable components
  * It allows for less nesting and lower specificity
  * It helps in building scalable stylesheets

**OOCSS**, or “Object Oriented CSS”, is an approach for writing CSS that encourages you to think about your stylesheets as a collection of “objects”: reusable, repeatable snippets that can be used independently throughout a website.

  * Nicole Sullivan's [OOCSS wiki](https://github.com/stubbornella/oocss/wiki)
  * Smashing Magazine's [Introduction to OOCSS](http://www.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/)

**BEM**, or “Block-Element-Modifier”, is a _naming convention_ for classes in HTML and CSS. It was originally developed by Yandex with large codebases and scalability in mind, and can serve as a solid set of guidelines for implementing OOCSS.

  * CSS Trick's [BEM 101](https://css-tricks.com/bem-101/)
  * Harry Roberts' [introduction to BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

Use variant of BEM with PascalCased “blocks”, which works particularly well when combined with components (e.g. React). Underscores and dashes are still used for modifiers and children.

**Example**

```jsx
// ListingCard.jsx
function ListingCard() {
  return (
    <article class="ListingCard ListingCard--featured">

      <h1 class="ListingCard__title">Adorable 2BR in the sunny Mission</h1>

      <div class="ListingCard__content">
        <p>Vestibulum id ligula porta felis euismod semper.</p>
      </div>

    </article>
  );
}
```

```css
/* ListingCard.css */
.ListingCard { }
.ListingCard--featured { }
.ListingCard__title { }
.ListingCard__content { }
```

  * `.ListingCard` is the “block” and represents the higher-level component
  * `.ListingCard__title` is an “element” and represents a descendant of `.ListingCard` that helps compose the block as a whole.
  * `.ListingCard--featured` is a “modifier” and represents a different state or variation on the `.ListingCard` block.

### ID selectors

While it is possible to select elements by ID in CSS, it should generally be considered an anti-pattern. ID selectors introduce an unnecessarily high level of [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) to your rule declarations, and they are not reusable.

For more on this subject, read [CSS Wizardry's article](http://csswizardry.com/2014/07/hacks-for-dealing-with-specificity/) on dealing with specificity.

### JavaScript hooks

Avoid binding to the same class in both your CSS and JavaScript. Conflating the two often leads to, at a minimum, time wasted during refactoring when a developer must cross-reference each class they are changing, and at its worst, developers being afraid to make changes for fear of breaking functionality.

We recommend creating JavaScript-specific classes to bind to, prefixed with `.js-`:

```html
<button class="btn btn-primary js-request-to-book">Request to Book</button>
```

### Border

Use `0` instead of `none` to specify that a style has no border.

**Bad**

```css
.foo {
  border: none;
}
```

**Good**

```css
.foo {
  border: 0;
}
```
**[⬆ back to top](#table-of-contents)**

## Sass

### Syntax

* Use the `.scss` syntax, never the original `.sass` syntax
* Order your regular CSS and `@include` declarations logically (see below)

### Ordering of property declarations

1. Property declarations

    List all standard property declarations, anything that isn't an `@include` or a nested selector.

    ```scss
    .btn-green {
      background: green;
      font-weight: bold;
      // ...
    }
    ```

2. `@include` declarations

    Grouping `@include`s at the end makes it easier to read the entire selector.

    ```scss
    .btn-green {
      background: green;
      font-weight: bold;
      @include transition(background 0.5s ease);
      // ...
    }
    ```

3. Nested selectors

    Nested selectors, _if necessary_, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

    ```scss
    .btn {
      background: green;
      font-weight: bold;
      @include transition(background 0.5s ease);

      .icon {
        margin-right: 10px;
      }
    }
    ```

### Variables

Prefer dash-cased variable names (e.g. `$my-variable`) over camelCased or snake_cased variable names. It is acceptable to prefix variable names that are intended to be used only within the same file with an underscore (e.g. `$_my-variable`).

### Mixins

Mixins should be used to DRY up your code, add clarity, or abstract complexity--in much the same way as well-named functions. Mixins that accept no arguments can be useful for this, but note that if you are not compressing your payload (e.g. gzip), this may contribute to unnecessary code duplication in the resulting styles.

### Extend directive

`@extend` should be avoided because it has unintuitive and potentially dangerous behavior, especially when used with nested selectors. Even extending top-level placeholder selectors can cause problems if the order of selectors ends up changing later (e.g. if they are in other files and the order the files are loaded shifts). Gzipping should handle most of the savings you would have gained by using `@extend`, and you can DRY up your stylesheets nicely with mixins.

### Nested selectors

**Do not nest selectors more than three levels deep!**

```scss
.page-container {
  .content {
    .profile {
      // STOP!
    }
  }
}
```

When selectors become this long, you're likely writing CSS that is:

* Strongly coupled to the HTML (fragile) *—OR—*
* Overly specific (powerful) *—OR—*
* Not reusable


Again: **never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should **never** need to do this.

**[⬆ back to top](#table-of-contents)**

## License

(The MIT License)

Copyright (c) 2015 Novatopo

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[⬆ back to top](#table-of-contents)**
