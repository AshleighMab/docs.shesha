---
sidebar_label: HTML Render 
title: HTML Render
---

# HTML Render

The HTML Render component lets a form script build up a piece of HTML and display it, instead of showing a fixed value. Use it when the markup itself needs to change based on form data - for example, a coloured status badge, a formatted summary line, or an image whose source depends on the record being viewed. It is also a great way to add static visual context or instructional content, such as styled text, headings, or images, between interactive form elements when the content doesn't need to depend on form data at all.

![Image](../Advanced/images/htmlrender1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Property name** `string`

HTML Render only reads this property's value - it never writes back to it. Binding a property here makes that field's current value available inside the Render HTML script as `value`.

#### **Render HTML** `function`

A script that returns the HTML string to render. Alongside the standard script variables (`data`, `globalState`, and so on), the script also receives `value`, the current value of the property set in **Property name**.

**Form type to use:** Any form type - the component is display-only and does not submit a value.

**Example - Show a coloured badge based on a status value:**

```javascript
const isActive = data.status === 1;
return `<span style="color: ${isActive ? 'green' : 'red'}; font-weight: bold;">${isActive ? 'Active' : 'Inactive'}</span>`;
```

:::warning An empty script renders a broken tag
If Render HTML is left blank, or the script returns a falsy value, the component falls back to rendering the literal string `<div><div/>` - an unclosed, nested `div`. Always return a valid HTML string from this script to avoid this fallback appearing on the form.
:::

#### **Hidden** `boolean`

A plain on/off checkbox that hides the component. It is not scriptable - there is no option to switch it to a JavaScript expression.

:::note
Style the rendered HTML directly in the markup you return from Render HTML - this component has no separate Appearance settings on this version.
:::
