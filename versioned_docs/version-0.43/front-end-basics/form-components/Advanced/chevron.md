---
sidebar_label: Chevron
title: Chevron
---

# Chevron

The Chevron component displays a horizontal pipeline of arrow-shaped stages, built from the items of a reference list, with the stage matching the component's current value highlighted. Use it to show progress through a fixed set of steps, such as an order or case moving through its status values.

![Image](./images/chevron1.png)

:::note Highlighting is visual only, clicks are configurable actions
The highlighted stage reflects the property's current value - it does not change automatically when a stage is clicked. Each stage is its own configurable action (the same action system used by the Button and Button Group components), so advancing to the next stage only happens if you configure that stage's action to do so (for example, running a script that updates the bound value).
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Property Name** `string`

The bound value is compared against each stage's item value to decide which stage is highlighted as active.


#### **Label Align** `string`

This property offers only two options: aligns the label to the **left** or **right** of the component.

#### **Hide Label** `boolean`

Hides the label without removing it, so the space it would occupy can still be reserved.

#### **Hidden** `function`

Controls whether the component is visible. Scriptable via the "fx" toggle, so it can be set as a plain on/off checkbox or replaced with a JavaScript expression that returns `true` or `false`.

#### **Description** `string`

Additional descriptive text for the component.

___

### Items

#### **Reference List** `object`

The reference list whose items become the Chevron's stages, one per list item. Selected via an autocomplete restricted to reference list entities.

:::tip
Reselect the Reference List here if you add, remove, or reorder items on the underlying reference list itself - the tooltip on this field in the designer calls this out explicitly.
:::

#### **Items** `object`

Opens a modal to configure each reference list item's stage: its label, icon, the action to run when it's clicked, its colour (when **Color Source** is set to **From RefList item**), and whether it's hidden from the pipeline.

:::note Shared configurator, leftover label
This modal's per-item editor is the same generic reference-list item configurator used elsewhere (for example, by the Kanban board's column configuration). As a leftover from that shared origin, the click-action field in this modal is labelled **onDrop Action configuration**, even though for Chevron it configures what happens when a stage is clicked, not a drag-and-drop event.
:::

___

### Styles

#### **Color Source** `object`

Controls where a stage's highlight colour comes from:

| Option | Behaviour |
|---|---|
| **Primary Color** *(default)* | Uses the application's theme primary colour. |
| **Custom Color** | Uses the **Active Color** set below. |
| **From RefList item** | Uses the colour configured on that stage in the **Items** modal. |

#### **Active Color** `string`

Only shown when **Color Source** is **Custom Color**. The highlight colour for the active stage; non-active stages show a faded version of this colour.

#### **Font Color** `string`

The text colour used on every stage, regardless of **Color Source**.

#### **Show Icons?** `boolean`

Shows or hides each stage's configured icon.

#### **Width** `number`

The width of each stage, in pixels. Defaults to `150`.

#### **Height** `number`

The height of each stage, in pixels. Defaults to `35`.

#### **Font Size** `number`

The font size used on each stage, in pixels. Defaults to `14`.
