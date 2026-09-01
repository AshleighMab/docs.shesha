---
sidebar_label: Reference List Status
title: Reference List Status
---

# Reference List Status

The Reference List Status component displays a value from a reference list as a coloured status badge, with an optional icon and item name, instead of showing it as plain text. Use it anywhere a record's status (for example, `Active`/`Inactive`, or a workflow stage) should stand out visually on the form.

![Image](../data-display/images/refliststatus1.png)

:::note Read-only display
This component only displays a reference list value - it does not let the user change it. It reads its value from the property you bind it to via **Property name** below.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Property name** `string`

Required - this is the field whose reference list value is displayed.

#### **Label align** `object`

Aligns the label to the `left` or `right` of the component.

#### **Hidden** `boolean`

A plain on/off toggle that hides the component - it cannot be switched into a JS expression, it is a fixed boolean only.

#### **Hide Label** `boolean`

When checked (the default), the **Label** text above is not shown.

___

### Customize Status

#### **Show Reflist Item Name** `boolean`

When checked (the default), displays the selected reference list item's name/display name.

#### **Show Icon** `boolean`

When checked, displays the reference list item's icon to the left of its name.

#### **Show Solid Background** `boolean`

When checked (the default), the component renders as a coloured, solid-background badge, with the icon and/or item name shown in white. When unchecked, the item's colour is used for the text instead of a background fill.

___

### RefList Source

#### **Reference list** `object`

The reference list that the displayed value belongs to. Required for the component to render anything other than a warning in the designer.
