---
sidebar_label: Container
title: Container
---

# Container

The Container component groups other components together into a single box. It doesn't bind to any data itself - use it purely as a layout wrapper, to arrange child components with flexbox or grid, and to apply a shared border, background, or size to the group as a whole.

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It is not scriptable.

#### **No Default Styling** `boolean`

If checked, the container's default styles and CSS classes are not applied, leaving its appearance fully up to your own Style/Custom CSS Class settings.

#### **Display** `object`

Sets the CSS `display` value used for laying out the container's children:

| Value | Behaviour |
|---|---|
| `flex` | Lays out children as a flexbox, enabling the Flex Direction, Flex Wrap, and Gap settings below. |
| `grid` | Lays out children as a CSS grid, enabling Grid Columns Count. |
| `inline-grid` | Same as grid, but the container itself behaves as an inline element. |
| `block` | Standard block layout - children stack top to bottom. |

#### **Flex Direction** `object`

Only shown when Display is `flex`. Sets the main axis of the flex layout: `row`, `row-reverse`, `column`, `column-reverse`, or one of the CSS-wide keywords (`inherit`/`initial`/`revert`/`revert-layer`/`unset`).

#### **Flex Wrap** `object`

Only shown when Display is `flex`. Controls whether flex children wrap onto multiple lines: `nowrap`, `wrap`, `wrap-reverse`, or a CSS-wide keyword.

#### **Gap** `string`

The spacing between children, for `flex` and `grid` layouts (hidden when Display is `block`). Accepts a plain number, or a CSS size such as `10px` or `20px 20px`.

#### **Grid Columns Count** `number`

Only shown when Display is `grid` or `inline-grid`. The number of columns the grid should have.

#### **Align Items** / **Align Self** / **Justify Content** / **Justify Self** / **Text Justify** `object`

Standard CSS alignment properties for flex/grid layouts, each offering the full set of CSS values for that property (for example, Justify Content offers `flex-start`, `flex-end`, `center`, `space-between`, `space-around`, `space-evenly`, and more).

#### **Justify Items** `object`

Sets the default `justify-self` for all of the container's children at once.

#### **Overflow** `object`

Controls how content that overflows the container's bounds is handled: `auto`, `hidden`, or `scroll`.

#### **Shadow Style** `object`

Adds a drop shadow to the container: `none`, `above` (shadow appears above the container), or `below` (shadow appears below).

___

### Border

#### **Border Width** `string`

The container's border width, in px.

#### **Border Type** `object`

The border line style: `solid`, `dashed`, or `dotted`.

#### **Border Color** `string`

The border colour, picked from a colour picker.

#### **Border Radius** `string`

The container's corner radius, in px or %.

___

### Background

#### **Background Type** `object`

Chooses whether the container has a solid `color` background or an `image` background.

#### **Color** `string`

Only shown when Background Type is `color`. The background colour, picked from a colour picker.

#### **Image Source Type** `object`

Only shown when Background Type is `image`. Where the background image comes from: `storedFileId` (a file already stored in Shesha), `url`, or `base64`.

#### **Background Size** `object`

Only shown when Background Type is `image`. Whether the image is sized with `contain` or `cover`.

#### **Background Repeat** `object`

Only shown when Background Type is `image`. Whether and how the image tiles: `repeat`, `no-repeat`, `repeat-x`, `repeat-y`, or `round`.

#### **Background Image URL** / **Upload Image** / **StoredFile ID**

Only one of these three shows at a time, matching whichever Image Source Type is selected above - a URL text field, an image upload control, or a Stored File ID text field respectively.

___

### Style

This panel is per-device (its fields can differ between desktop, tablet, and mobile).

#### **Width** / **Min Width** / **Max Width** / **Height** / **Min Height** / **Max Height** `string`

The container's size, accepting `%`, `px`, or `vw` values.

#### **Custom CSS Class** `string`

A custom CSS class name to add to the container.

#### **Wrapper Style** `function`

A script that returns the style to apply to the container's outer wrapper element, as an object. Only has access to `data`. Hidden when **No Default Styling** is checked.
