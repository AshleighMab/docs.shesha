---
sidebar_label: Table View Selector
title: Table View Selector
---

# Table View Selector

The Table View Selector component lets users switch between predefined named filters (or "views") of a Data Table with a single click, instead of building a filter manually. Configure one or more named filters, and the user picks between them.

![Image](../tables-lists/images/tableSelector2.png)

_Implementation can be found [here](/docs/front-end-basics/how-to-guides/filtering)._

:::warning Requires a Data Table Context
Table View Selector only works inside a [Data Table Context](/docs/front-end-basics/form-components/tables-lists/datatable-context) component. If it is placed outside one, it renders a warning in the designer instead: "Table view selector must be used within a Data Table Context".
:::

---

## Properties

Table View Selector has a much smaller settings panel than most components - it does not use the Common, Appearance, Data, Validation, or Events tabs described in the [common properties](/docs/front-end-basics/form-components/common-component-properties). Only the three fields below are available.

### **Filters** `array`

The list of named views to offer. Each entry defines a Title, an optional Tooltip (shown as a help icon next to the title, both in the designer list and at runtime), a filter (built the same way as any other query filter, and able to reference `data`, `globalState`, `pageContext`, and other contexts dynamically), and its own optional Permissions - a view is only offered to users who hold at least one of its own listed permissions.

At runtime, a single configured filter is shown as a plain title with no way to switch; with two or more filters, the title becomes a dropdown that the user clicks to pick a different view.

:::note At least one filter is required
If no filters are configured, or all have been evaluated as unavailable, the component shows "Please make sure that you have at least 1 filter" in the designer, and renders nothing at runtime.
:::

### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It has no JavaScript-expression mode.

### **Permissions** `object`

Restricts visibility of the whole component. Each individual filter can additionally have its own Permissions, restricting just that one view.
