---
sidebar_label: Table Pager 
title: Table Pager
---

# Table Pager

The Table Pager component breaks a large dataset into pages, letting users navigate through a Data Table's rows instead of loading them all at once. It is meant to be used alongside a [Data Table Context](/docs/front-end-basics/form-components/tables-lists/datatable-context) component.

![Image](../tables-lists/images/tablepager1.png)

:::note No warning if used incorrectly
Table Pager won't warn you if you place it outside a Data Table Context - it renders normally either way, with no error message. It just won't be useful there, since there's no data for it to page through. Always place it inside a Data Table Context, as described above.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Component Name** `string`

Required on this component, and not scriptable.

___

### Page Sizes

#### **Show Size Changer** `boolean`

Toggles the page size selector dropdown. Defaults to on.

#### **Show Total Items** `boolean`

Toggles the display of the total item count. Defaults to on.

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It has no JavaScript-expression mode.
