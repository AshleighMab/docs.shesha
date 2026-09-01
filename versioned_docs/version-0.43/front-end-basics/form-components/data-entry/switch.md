---
sidebar_label: Switch
title: Switch
---

# Switch

The Switch component is a simple, stylish way to toggle a boolean field between ON and OFF. Use it for feature flags, settings, and any yes/no field where a toggle reads more naturally than a checkbox.

![Image](./images/switch.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Label** `string`

The text shown next to the switch, as a plain text field rather than a combined label toggle - use **Hide Label** below to remove the label entirely.

#### **Hidden** `boolean`

A plain checkbox that hides the component when checked.

#### **Hide Label** `boolean`

Hides the label without hiding the component itself.

___

### Style

#### **Size** `object`

Selects the size of the switch:

| Option | Behaviour |
|---|---|
| **Default** | The standard size for the switch. This is the default. |
| **Small** | A compact version of the switch. |
