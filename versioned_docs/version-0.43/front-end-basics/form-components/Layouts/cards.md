---
sidebar_label: Card 
title: Card
---

# Card

The Card component is a styled container for grouping other components together under an optional heading. Use it to visually separate a logical section of a form, such as a summary block or a group of related fields.

![Image](../Layouts/images/cards1.png)

![Image](../Layouts/images/cards2.png)

:::note Two drop areas
A Card has two separate areas you can drag components into: the main **content** area, and a **header** area alongside the Heading text. Dropping components (for example, a button) into the header area places them next to the heading, not inside the card body.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Component Name** `string`

Bound through a property-autocomplete input rather than a plain text field, but serves the same purpose as elsewhere: a unique identifier for this component within the form.

#### **Heading** `string`

Text displayed at the top of the card, next to anything dropped in the header area.

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. 

#### **Hide Heading** `boolean`

Hides the Heading text (and the header area next to it), even if one is set.

___

### Style

These settings can be configured separately per device (desktop, tablet, mobile).

#### **Custom CSS Class** `string`

A custom CSS class name to add to the card.

___

### Visibility

#### **Hide When Empty** `boolean`

Hides the entire card when it has no visible content, for example when every child component inside it is currently hidden by its own conditions.
