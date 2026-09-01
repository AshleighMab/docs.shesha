---
sidebar_label: Validation Errors
title: Validation Errors
---

# Validation Errors

The Validation Errors component displays the current form's validation errors as a single alert banner. Add it to a form when you want a centralised summary of what's wrong, instead of relying only on the red messages shown under each individual field.

![Image](../data-display/images/validationerror1.png)

:::note Only visible with errors at runtime
The component always shows validation errors for the whole form, not a specific field, and it isn't tied to a Property Name. In the form designer, it shows a placeholder message instead of real errors ("Validation Errors (visible in the runtime only)") - open the form at runtime and trigger a validation failure to see the actual banner.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Display

#### **Component Name** `string`

An internal name used to identify this component within the form definition. It is not scriptable and is not tied to a bound entity property.

:::note No Hide setting
Validation Errors does not expose a Hide property on this version - it is designed to always be present on the form so validation feedback is never accidentally hidden.
:::

___

### Style

#### **Custom CSS Class** `string`

A custom class name to apply to the component, for styling via your own stylesheet.

#### **Style** `function`

A script that returns the style of the component as an object, conforming to `CSSProperties`. The standard `data` and `globalState` variables are available inside it. Unlike Custom CSS Class and Margin and Padding, this script's result is actually applied to the component.

#### **Margin and Padding** `object`

:::warning Margin and Padding has no visible effect
On this version, the Margin and Padding value is not applied when the component renders - only **Style** and **Custom CSS Class** actually affect the component's appearance. Use the Style script if you need spacing around this component.
:::
