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

    p { ... }        # element selector (all <p> elements)
    #para1 { ... }   # id selector (all elements with id="para1")
    .centre { ... }  # class selector (all elements with class="centre")

    p.center { ... }  # all <p> elements with class="centre"
                      # in which case multiple styles will be applied

## em and rem

- use `rem` with font-size
  - it looks at the root or HTML element for `font-size`
  - no parent/child calculations (unlike `em`)
- use `em` with padding 
  - adjusts the padding based on font-size of the element
- use `rem` with margin for consistent space among adjacent elements

## Units

    font-size: rem
    width: % in combination with a max-width, ch
    height: question "do i really need to set height" if yes -> use a min-height
    padding/margin: rem or em, kevin often uses em for padding of buttons
    media queries: em

## flex

https://yoksel.github.io/flex-cheatsheet/
https://devtoolstips.org/tips/en/outline-everything/

most common props & values are:
    parent:
        display: flex
        justify-content: 'center' | 'space-between'
        align-items: 'center' | 'space-between'
    child:
        flex-basis: 30%    # use this instead of width with flexbox

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

    


