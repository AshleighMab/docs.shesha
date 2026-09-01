---
sidebar_label: Markdown
title: Markdown
---

# Markdown

The Markdown component displays richly formatted text written in Markdown syntax - headings, bold and italic text, lists, links, images, and fenced code blocks with syntax highlighting. Use it for documentation, help text, or any other content that needs more formatting than a plain Text component allows.

![Image](./images/markdown1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Property Name** `string`

This is only used as a fallback - if **Content** is empty, the component displays the value bound to this property instead.

#### **Content** `string`

The Markdown text to display, written directly in a Markdown-aware code editor (not a JavaScript expression). It is evaluated with the form's `data` and `globalState` before rendering, so `{{ }}`-style placeholders inside the text are substituted with real values.

**Form type to use:** Any form type - the component is display-only and does not submit a value.

**Example - Mix static Markdown with a data placeholder:**

```markdown
## Order Summary

Order **{{data.orderNumber}}** was placed on {{data.orderDate}}.
```

If Content is left empty, the component falls back to whatever value is bound via **Property Name** instead.

:::note Empty content warning in the designer
If both Content and the bound Property Name resolve to nothing, the component shows an inline warning in the form designer ("Please make sure you enter the content to be displayed here!") instead of a blank space. This warning is designer-only and will not appear to end users at runtime.
:::

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. Unlike the common Hide setting, it is not scriptable - there is no option to switch it to a JavaScript expression.

