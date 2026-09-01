---
sidebar_label: Number Field
title: Number Field
---

# Number Field

The Number Field component is a numeric input, letting users enter and edit numbers with a configurable minimum/maximum range and an increment step, with optional high-precision decimal support. When the form is read-only, it displays the number pre-formatted instead of as an editable input.

![Image](./images/numberfield1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Property Name** `string`

Only properties whose data type is a number are offered when browsing entity properties to bind to.

:::tip Binding fills in Min/Max automatically
When Property Name is bound to an entity property, **Min Value** and **Max Value** (in the Validation panel below), along with the component's Label and Description, are pre-filled from that property's own metadata - you don't need to set them manually unless you want to override them.
:::

#### **Label Align** `object`

Number Field-specific left/right alignment choice for the label text.

#### **High Precision** `boolean`

Switches the input to string-based numeric handling, for entering decimals with more precision than standard floating-point numbers safely support.

#### **Step** `number / string`

How much the value changes per click of the input's up/down arrows. This field changes type depending on **High Precision**: a plain number field when High Precision is off (default `1`), or a text field when High Precision is on (default `"0.1"`).

:::warning Step value while High Precision is on
The Step text field shown while High Precision is on is not actually read by the component - the step used at runtime always comes from the numeric Step value that was set while High Precision was off. If you need a specific step (e.g. `0.01`) with High Precision enabled, temporarily turn High Precision off, set the numeric Step, then turn High Precision back on.
:::

#### **Hide Label** `boolean`

Hides the component's label without hiding the component itself.

#### **Hide Border** `boolean`

Removes the input's border, rendering it borderless.

___

### Data

#### **Default Value** `number`

A default numeric value used when the form has no existing value for this field. Evaluated as a script with `formData`, `formMode`, and `globalState` exposed.

___

### Events

#### **On Change** `function`

Fires when the value changes. Alongside the standard event variables, `value` (the component's current value) is also exposed.

___

### Validation

#### **Min Value / Max Value** `number`

Restrict entries to within a specified numeric range.

