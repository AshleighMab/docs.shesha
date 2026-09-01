---
sidebar_label: HTML Render
title: HTML Render
---

# HTML Render

The HTML Render component lets a form script build up a piece of HTML and display it, instead of showing a fixed value. Use it when the markup itself needs to change based on form data - for example, a coloured status badge, a formatted summary line, or an image whose source depends on the record being viewed. It is also a great way to add static visual context or instructional content, such as styled text, headings, or images, between interactive form elements when the content doesn't need to depend on form data at all.

![Image](./images/htmlrender1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Common

#### **Property Name** `string`

HTML Render only reads this property's value - it never writes back to it, so binding a Property Name here is a way to make a field's value available inside the Render HTML script as `value`, not to edit that field.

#### **Render HTML** `function`

A script that returns the HTML string to render. Alongside the standard script variables (`data`, `globalState`, and so on), the script also receives `value`, the current value of the property set in **Property Name**.

**Form type to use:** Any form type - the component is display-only and does not submit a value.

**Example - Show a coloured badge based on a status value:**

```javascript
const isActive = data.status === 1;
return `<span style="color: ${isActive ? 'green' : 'red'}; font-weight: bold;">${isActive ? 'Active' : 'Inactive'}</span>`;
```

:::warning An empty script renders a broken tag
If Render HTML is left blank, or the script returns a falsy value, the component falls back to rendering the literal string `<div><div/>` - an unclosed, nested `div`. Always return a valid HTML string from this script to avoid this fallback appearing on the form.
:::

___

### Appearance

HTML Render's Appearance tab exposes a single **Custom Styles** script, which returns the style to apply to the element as an object. Style the rendered HTML directly in the markup you return, or via this script.
