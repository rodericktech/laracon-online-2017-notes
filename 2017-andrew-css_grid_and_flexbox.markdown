CSS Layout in 2017 with Grid and Flexbox
----------------------------------------

_Rachel Andrew, Laracon Online 2017_

### Introduction

Rachel is:

- CSS working group invited expert
- Google Dev Expert for Web Tech
- Co-Founder of Perch CSS (grabaperch.com)
- Contact: m2@rachelandrew.co.uk

She will discuss new things in CSS layout technology.

- Float, inline-block, display: table, absolute & relative positions
- Tons of frameworks
    - Frameworks remove some of the headache

Modules that will redefine how we do web layout:

- Flexbox, CSS Grid Layout, Box Alignment

### New CSS for Layout

Newer tools allow for actual centering, more layout control, responsive by default. They give items a way to relate to one another.

A big problem with modern layout tools is that all items are independent and have no idea about how they should 'react' to one another.

An earlier answer to this issue was the use of 'display:table' for everything.

Flexbox and Grid take the display:table concept and flesh it out into something more usable, friendly and flexible.

[Flexbox and Grid examples of very basic relational layout]

### Separation of Source and Display

**Flex** example

~~~~
nav ul {
    display: flex;
    justify-content: space-between;
    flex-direction: row-reverse;
}
~~~~

**Grid** example

It's possible to use automatic placement rules instead of manual specification, which
is the default behavior.

~~~~
.wrapper {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-rows: auto;
}

.landscape {
    grid-column-end: span 2;
}
~~~~

Don't use Grid or Flexbox as an excuse to forget about the source order. Even worse,
don't strip out semantic elements to turn everything into a grid / flex item.

_Use the ordering only for visual (not logical) reordering of content._

https://vimeo.com/180566024 - Leonie Watson video on CSS accessibility

### Control of Alignment

Box Alignment Module Level 3 - https://drafts.csswg.org/css-align

This takes all the alignment out of the CSS spec and drops it in its own spec.

It's 2017. We can now center things.  :D

- justify-content
- align-content
- justify-self
- align-self
- justify-items
- align-items

Grid has two axes to align against, whereas Flex uses one, making Grid possibly easier
to understand and use. [Cheat sheet slide]

### Responsive By Default

Grid and Flexbox were 'born' post-responsive world and are fluid out of the box.

No longer do we have to do complex calculations to make layouts work in multiple contexts.

~~~~
nav ul{
    display: flex;
    justify-content: space-between;
}
~~~~

**Flex property**:

- flex-grow (add space)
- flex-shrink (remove space)
- flex-basis (initial size before grow / shrink)

~~~~
.wrapper {
    display: flex;
}
.wrapper li {
    flex: 1 1 300px;
    min-width: 1px;
}
. wrapper li:nth-child(3) {
    flex-grow: 2 1;
}
~~~~

https://madebymike.com.au/demos/flexbox-tester/ (flexbox calculator)

### CSS Grid Layout fr Unit

The way this responsiveness works in Grid is via the fr unit.

~~~~
.wrapper {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    OR
    grid-template-columns: 600px 1fr 1fr;
    OR
    grid-template-columns: 600px 1fr 3fr;
    OR
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}
~~~~

Flexbox is for 1-dimensional layout (row or column); Grid is for 2-dimensional layout.

_Imitate Flexbox auto-fill with Grid:_

~~~~
.wrapper {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}
~~~~

### Bringing It All Together

[Examples of rows expanding to contain new content rather than overflowing]

~~~~
.home-hero {
    display: grid;
    grid-gap: 1px;
    grid-auto-rows: minmax(150px, auto); /* prevents overflow */
}
~~~~

Flexbox example of layout resilience:

~~~~
.special {
    display: flex;
    flex-direction: column;
}

.special h3 {
    margin-top: auto;
}
~~~~

- http://gridbyexample.com
- http://gridbyexample.com/browsers
- http://caniuse.com/#feat=flexbox
- http://caniuse.com/#feat=css-grid

- Firefox 52 is the first browser (just released) with support for Grid.
- Chrome 57 will support Grid (next week)
- Safari will ship with Grid support in March / April

IE and Edge still use their early implementation of CSS Grid.

### Feature Queries

This is how we will deal with older browsers when using Flex / Grid.

~~~~
@supports (display: grid) {
    .has-grid {
        /* CSS for grid browsers here */
    }
}
~~~~

Write CSS for browsers without support
Override those properties in feature queries

**Grid Tips**

- Floated items that become grid / flex lose the float behavior
- vertical-align has no effect on a grid item
- inline-block or block become grid items
- Overrides change widths, margins and padding
- CHECK ITEM WIDTHS if something in grid / flex uses an unexpected size

http://caniuse.com/#feat=css-featurequeries

_You can use feature queries to detect support for anything new in CSS._

Don't forget to have fun.  :)

https://rachelandrew.co.uk/speaking/event/laracon-online

