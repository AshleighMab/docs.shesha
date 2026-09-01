---
sidebar_label: Link
title: Link
---

# Link

The Link component renders an HTML link (`<a>` tag) that navigates users to another page, an external URL, or a section of your application. It can show simple text, or - when **Has Children** is enabled - wrap other components so the whole group becomes clickable.

![Image](./images/link1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Content** `string`

The plain text shown inside the link when **Has Children** is off. Unlike **Href** below, this is a plain string - it is rendered as-is and does not support `{{ }}` placeholders.

#### **Href** `string`

The URL the link navigates to. This value is evaluated as a template string against the form's data, so it supports `{{ }}` placeholders.

**Form type to use:** Any form type - the component is display-only and does not submit a value.

**Example - Link to a record's details page using its ID:**

```
/dynamic/membership/member-details?id={{data.id}}
```

#### **Target** `object`

Controls the standard HTML anchor `target` attribute - the dropdown's options are shown literally as `_blank`, `_parent`, `_self`, and `_top` (not friendly labels like "Blank" or "Self"):

| Option | Behaviour |
|---|---|
| `_blank` | Opens the link in a new tab. |
| `_parent` | Opens the link in the parent frame. |
| `_self` *(default)* | Opens the link in the same frame. |
| `_top` | Opens the link in the topmost frame. |

#### **Hidden** `boolean`

A plain on/off checkbox that hides the component - it only supports a fixed true/false value, not a scriptable expression.

#### **Has Children** `boolean`

When enabled, the Link no longer shows **Content** as text. Instead, it becomes a container you can drop other components into, and the whole group is wrapped in the link. This reveals the **Content** panel described below, and switches **Content Panel**'s **Direction**/**Justify**/**Align** settings into use for laying out the nested components.

___

### Content

This panel only appears when **Has Children** is enabled on the Display panel above, and controls how the nested components are laid out.

| Setting | What it controls |
|---|---|
| **Direction** | Lays out the nested components `horizontal`ly or `vertical`ly. |
| **Justify Content** | CSS `justify-content` value for the row. Only shown when Direction is `horizontal`. |
| **Align Items** | CSS `align-items` value for the row. Only shown when Direction is `horizontal`. |
| **Justify Items** | CSS `justify-items` value for the row. Only shown when Direction is `horizontal`. |
| **Custom CSS Class** | A custom CSS class name to apply to the nested-components container. |

:::note Not visible while designing
In the form designer, a Has Children link shows its nested components directly (without the surrounding clickable `<a>` tag), so you can select and edit them. The link only wraps them at runtime.
:::


