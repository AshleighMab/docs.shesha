---
sidebar_label: Rate
title: Rate
---

# Rate

The Rate component lets users give a star (or custom icon) rating by clicking one of a row of repeated icons. It binds to a numeric field on your entity.

![Image](../data-entry/images/rate1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Label Align** `object`

Aligns the label text to the left or right of the component.

#### **Count** `number`

The number of rating icons to display (for example, 5 for a typical 5-star rating).

#### **Icon** `string`

Picks a single icon to use for every rating position, replacing the default filled star. This is one icon for the whole row, not a different icon per position.

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It is not scriptable.

#### **Hide Label** `boolean`

Hides the label text, showing only the rating icons.
