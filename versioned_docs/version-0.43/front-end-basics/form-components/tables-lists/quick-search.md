---
sidebar_label: Quick Search
title: Quick Search
---

# Quick Search

The Quick Search component adds a free-text search box that filters the rows of a Data Table by matching against all of its columns at once. Use it when users need a fast, general-purpose way to find rows without setting up column-specific filters.

![Image](../tables-lists/images/quicksearch1.png)

:::warning Requires a Data Table Context
Quick Search only works inside a [Data Table Context](/docs/front-end-basics/form-components/tables-lists/datatable-context) component. If it is placed outside one, it renders a warning in the designer instead: "Quick Search must be used within a Data Table Context".
:::

---

## Properties

Quick Search has a much smaller settings panel than most components - it does not use the Common, Appearance, Data, Validation, or Events tabs described in the [common properties](/docs/front-end-basics/form-components/common-component-properties). Only one field is available, under a **Display** panel.

### Display

#### **Block** `boolean`

When enabled, the search input stretches to fill the full width of its container, instead of sizing itself to its content. Defaults to off.
