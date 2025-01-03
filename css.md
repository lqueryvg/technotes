# CSS

## Syntax

    selector { property:value; ... }

Example:

    p {             # p is an element selector
      color: red;   # color is the property, red is the value
    }

## class

    class="centre"
    class="centre large"

## Selectors

- `p { ... }` _element_ selector (all `<p>` elements)
- `#para1 { ... }` _id_ selector (all elements with `id="para1"`)
- `.centre { ... }` _class_ selector (all elements with- `class="centre")
- `p.center { ... }` all `<p>` elements with `class="centre"`; multiple styles will be applied

## Popular properties

- `color`: text color
- `font-size`:` size of font
- `background-color`: background color
- `margin`: space around element
- `padding`: space within element
- `border`: border around element
- `display`: display behavior of element

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
- `width`: use `%` in combination with a `max-width`, ch
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
