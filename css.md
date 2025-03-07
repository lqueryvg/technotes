<!-- markdownlint-disable MD046 -->

# CSS

## Syntax

    selector { property:value; ... }

Example:

```css
p {
  /* p is an element selector */
  color: red; /*color is the property, red is the value */
}
```

## class

    class="centre"
    class="centre large"

## Popular properties

- `color`: text color
- `font-size`:` size of font
- `background-color`: background color
- `margin`: space around element
- `padding`: space within element
- `border`: border around element
- `display`: display behavior of element
- `strong`: strong text, usually bold
- `em`: emphasis text, usually italic

## `font-family`

- list of fonts to be used when displaying text
- priorty list separated by commas
- fonts can be generic (browser defaults) or string names which must be
  downloaded or available in the browser
- Font selection checks each character against the list, using later fonts for
  missing glyphs.
- Styles, variants, and sizes also influence font choice.
- always include a generic family name for guaranteed fallback
- using generic names could look different on different browsers
- generic name eg: monospace, cursive, serif, sans-serif, ui-rounded

  monospace
  "Helvetica", sans-serif
  "Helvetica"

## shorthand property

Example:

```css
font-family: fantasy;
font-size: 13px;
font-style: italic;
```

can be written as:

```css
font: italic 13px fantasy;
```

But you need to put things in the right order.

## em and rem

- `em` is relative to font size of parent
- `rem` is relative to font size of root element

- use `rem` with font-size
  - it looks at the root or HTML element for `font-size`
  - no parent/child calculations (unlike `em`)
- use `em` with padding
  - adjusts the padding based on font-size of the element
- use `rem` with margin for consistent space among adjacent elements

## Units

- `font-size`: use rem
- `width`: use `%` in combination with a `max-width`
- `ch`: the with of a `0` in current font, `60ch` is recommended for text
- `height`: question "do i really need to set height" if yes, use a `min-height`
- `padding`/`margin`: use `rem` or `em`; try `em` for button padding
- media queries: `em`

## flex

https://yoksel.github.io/flex-cheatsheet/
https://devtoolstips.org/tips/en/outline-everything/

### Common props & values

#### In the parent

- `display: flex`
- `flex-direction`: set main axis; `row` or `column`, default is `row`
- `justify-content`: main axis alignment; `center`, `space-between`
- `align-items`: cross axis alignment; `center`, `space-between`

#### In the child

- `flex-basis: 30%`: use this instead of width with flexbox

Everything centred:

    display: flex;
    justify-content: center;
    align-items: center;

## grid

A square grid:

    display: grid;
    justify-content: center;
    align-content: center;
    grid-template-columns: auto auto;

## Property inheritance

- Some are inherited or have an initial default value.

- Others are not inherited and default to the computed value of its
  containing element

### Inherited properties

Generally: text styling.

- font, font-family, font-size, font-style, font-weight, letter-spacing, word-spacing

- line-height, text-align text-indent, text-justify, text-shadow, text-transform

- color, cursor, visibility

### Non inherited properties

Generally: position, size and new CSS properties.

- All of the following types of properties:
  background, border, Flexbox, Grid, margin, overflow,
  padding, position, sizing, transition
- text-decoration
- box-sizing
- text-overflow
- float
- transform
- transform-origin
- transform-style

## Misc

```css
/* Styling Text */
font-family: "Arial", sans-serif; /* fallback to sans-serif */
font-size: 16px; /* eg. px, em, rem, % */
font-weight: bold; /* eg. normal, bold, bolder, lighter, 100-900 */
font-style: italic; /* eg: normal, italic, oblique */
text-decoration: underline; /* eg. none, underline, overline, line-through */
color: #333; /* Hex color code */
line-height: 1.5; /* Unitless value, relative to font size */
text-align: center; /* horiz alignment, eg: left, right, justify */
letter-spacing: 1px; /* horiz space between letters, can be negative */
text-indent: 20px; /* Indent first line of text */
text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3); /* Add text shadow
                 h-offset, v-offset, blurr-radius, color */
text-transform: uppercase; /* Transform text case; other options:
                        lowercase, capitalize, none */

/* Box Model */
width: 300px;
height: 200px;
padding: 20px; /* Padding between content and border */
margin: 10px; /* Margin outside border */
border: 1px solid #ccc; /* Border width, style, and color */
box-sizing: border-box; /* Include padding/border in total width/height */
box-sizing: content-box; /* (default) only content size is included */

/* Background */
background-color: #f0f0f0;
background-image: url("image.jpg"); /* image path */
background-size: cover; /* Scale image to cover container
                          also: contain, auto, <dimensions> */
background-position: center; /* Position background image
                               also: top, bottom, left, right, percentage */

/* Positioning */
position: relative; /* relative to normal position */
position: absolute; /* relative to nearest positioned ancestor */
position: fixed; /* relative to viewport */
top: 10px;
left: 20px;
bottom: 10px;
right: 20px;
z-index: 1; /* Stack order; higher values are on top */
display: block; /* block-level element; takes full width of parent */
display: inline; /* Inline element; only takes necessary width */
display: inline-block; /* like inline but can have width/height */
display: flex; /* Flex container; enables flexbox layout */
flex-direction: row; /* eg. column, row-reverse, column-reverse */
justify-content: center; /* flex align on main axis, also: flex-start,
                           flex-end, space-between, space-around */
align-items: center; /* flex align on cross axis, also:
                       flex-start, flex-end, stretch, baseline */
float: left; /* Position to left of container, allows other content
        to wrap around it; other values: right, none */

/* Other */
opacity: 0.8; /* opacity; 0 = transparent, 1 = opaque */
cursor: pointer; /* Set mouse cursor to a pointer when over element */
overflow: hidden; /* Hide content overflowing boundaries,
                    also: visible, scroll, auto */
overflow-wrap: break-word; /* break long words in paragraph */
overflow-x: auto; /* in a pre block, allow horizontal scrolling if long */
transition: all 0.3s ease; /* Add transition effect:
                       property, duration, timing-function */
transform: rotate(
  45deg
); /* Rotate 45 degrees; also: scale(), translate(), skew() */
box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.2); /* Add shadow
   values: horizontal offset, vertical offset, blur radius, color */
border-radius: 5px; /* Round corners; higher value is more rounded */
visibility: visible; /* without affecting position. Also: hidden, collapse */
```

## Selectors

```css
/* Element Selectors */
p {} /* all <p> elements */
h1, h2, h3 {} /* all <h1>, <h2>, and <h3> elements */
ul li strong {} /* <strong> within <li> within <ul> elements */

/* Class Selectors */
.my-class {} /* all elements with class="my-class" */
p.center {} /* all <p>` elements with `class="centre" */
p.center.red {} /*  all `<p>` elements with `class="centre"` and `class="red" */

/* ID Selectors */
#my-id {.} /* the element with id="my-id" (should be unique) */
#header {} /* the element with id="header" */
#footer {} /* the element with id="footer" */

/* Universal Selector */
* {} /* all elements on the page (use with caution) */


/* Attribute Selectors */
[type="text"] {} /* elements with type="text" attribute */
[href] {} /* elements with an href attribute */
[title="My Title"] {} /* elements with title="My Title" */
[lang|="en"] {} /* lang starts with "en" (e.g., en, en-US, en-GB) */
[class~="button"] {} /* class contains "button" (e.g., class="main-button") */
[href^="https"] {} /* elements with href starting with "https" */
[href$=".pdf"] {} /* elements with href ending with ".pdf" */
[data-value="123"] {} /* elements with data-value attribute equal to 123 */

/* Combinators */
div p {} /* <p> elements that are descendants of <div> elements */
div > p {} /* <p> elements that are direct children of <div> elements */
div + p {} /* first <p> element immediately after a <div> (adjacent sibling) */
div ~ p {} /* <p> elements preceded by a <div> element (general sibling) */

/* Pseudo-classes */
a:hover {} /* <a> elements when the mouse hovers over them */
a:active {} /* <a> elements when they are being clicked */
a:visited {} /* <a> elements that have been visited */
p:first-child {} /* the first <p> element within its parent */
p:last-child {} /* the last <p> element within its parent */
p:nth-child(2) {} /* the second <p> element within its parent */
p:nth-child(2n) {} /* all even <p> elements within its parent */
p:nth-child(odd) {} /* all odd <p> elements within its parent */
p:nth-of-type(3) {} /* the third <p> element of its type within its parent */
input:focus {} /* the <input> element that is currently focused */
::selection {} /* the portion of an element that is selected by the user */

/* Pseudo-elements */
p::first-line {} /* the first line of text within <p> elements */
p::first-letter {} /* the first letter of text within <p> elements */
p::before {} /* Inserts content before <p> elements */
p::after {} /* Inserts content after <p> elements */
```

## Specificity

Highest priority to lowest:

- `!important`
  - eg. `color:red !important;`
- inline `style` props
- selectors:
  - `#` (id)
  - `.`, `[]`, `:` (class, attribute & pseudo-class)
  - `h1`, `::` (element, pseudo-element)

Conflicts:

- most specific selector wins
- if equal, the last rule declared wins
