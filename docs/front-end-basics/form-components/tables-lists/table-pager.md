---
sidebar_label: Table Pager
title: Table Pager
---

# Table Pager

The Table Pager component shows pagination controls (page numbers, page size, and a total-item count) for a DataTable or DataList on the same form. It must be placed inside a [Data Table Context](/docs/front-end-basics/form-components/tables-lists/datatable-context) - it reads the current page, total rows, and page size directly from that context rather than from its own data.

![Image](../tables-lists/images/tablepager1.png)

:::warning Requires a Data Table Context
If you add a Table Pager outside a Data Table Context, the designer raises a validation error and the component falls back to a disabled, greyed-out placeholder pager instead of a working one. The same placeholder also shows if the Data Table Context has no DataTable or DataList configured in it yet.
:::

:::note Hidden automatically on narrow screens
Regardless of the **Show Size Changer** and **Show Total Items** settings below, both are hidden automatically when the screen is phone-sized, to save space.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Common

#### **Show Size Changer** `function`

Toggles the page size selector dropdown. Defaults to enabled. Scriptable via the "fx" toggle.

#### **Show Total Items** `function`

Toggles the display of the total item count. Defaults to enabled. Scriptable via the "fx" toggle.