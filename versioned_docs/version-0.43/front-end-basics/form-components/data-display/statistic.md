---
sidebar_label: Statistic
title: Statistic
---

# Statistic

The Statistic component displays a single numeric value with an optional title above it and an optional icon before or after the number. Use it for key figures on a dashboard or details view, such as a total, a count, or a score.

![Image](./images/statistic1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Title** `string`

Text shown above the value, using antd's built-in Statistic title.

#### **Hidden** `boolean`

A plain checkbox that hides the component when checked.

#### **Prefix Icon** `object`

An icon shown before the value, chosen from Shesha's icon picker.

:::note Icon only, not text
Despite the plain "Prefix"/"Suffix" names you may see elsewhere, this component only supports an **icon** before or after the value, not free text.
:::

#### **Suffix Icon** `object`

An icon shown after the value, chosen from Shesha's icon picker.

___

### Style

Statistic's Style tab has two dedicated style scripts:

#### **Title Style** `function`

A script that returns the CSS style of the title, as an object conforming to `CSSProperties`. The script receives `data` (the form's values).

#### **Value Style** `function`

A script that returns the CSS style of the value, as an object conforming to `CSSProperties`. The script receives `data` (the form's values).

**Form type to use:** Any form type - Statistic is display-only for styling purposes, though it can still write a value back via Property Name.

**Example - Colour the value red when negative:**

```javascript
return {
  color: data.balance < 0 ? 'red' : 'green',
  fontWeight: 'bold',
};
```

:::note No decimal/precision control
The underlying antd Statistic component supports a `precision` (decimal places) setting, but Shesha's designer does not expose it here - the value is displayed exactly as stored. Format the number yourself (e.g. via a calculated property) if you need a fixed number of decimal places.
:::
