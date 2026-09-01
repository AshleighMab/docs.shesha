---
sidebar_label: Proxying Input Variables 
sidebar_position: 3
title: Proxying Input Variables
---

# Proxying Input Variables

Many component properties can be set either with a plain UI control, such as a checkbox, or with a JavaScript expression instead - for example, a component's Hidden property can be a simple on/off switch, or a script that decides visibility based on form data. The input variables available inside these scripts, such as `contexts`, `data`, `form`, and `http`, are not passed in as plain objects - they are wrapped in a proxy that tracks which fields the script actually reads, so Shesha only recalculates the property when a field it depends on changes.

![Image](../front-end-basics/configured-views/images/ProxyVariables.png)

---

## Why Proxying Exists

Without this optimization, changing any single field would re-run the JS expression for every scripted property on every component on the form, since there would be no way to know which fields a given script actually depends on. That barely matters for a form with a handful of components, but a form with dozens of fields, tabs, and sub-forms would visibly lag on every keystroke if every scripted property recalculated on every change.

To avoid that, every input variable passed into a scripted property (`data`, `contexts`, and so on) is wrapped in a proxy. Reading a field through the proxy returns its real value and records that the script "touched" that field. The next time any field changes, Shesha only re-runs the scripts that touched it.

**Example - Only `firstName` changes trigger recalculation:**

Suppose a form has three fields, `firstName`, `lastName`, and `description`, and the `description` field's Hidden property is scripted as:

```javascript
return data.firstName?.length > 3;
```

Because this script only ever reads `data.firstName`, Shesha only recalculates `description`'s Hidden property when `firstName` changes. Editing `lastName` does not trigger it.

---

## Working With Proxied Data Directly

Because the proxy tracks reads and writes transparently, you can work with form data and contexts directly, without needing helper methods like `setFieldValue`, `setFieldsValue`, or `setFormData`:

```javascript
data.firstName = data.firstName + ' test';
```

:::warning Logging a proxied variable logs the proxy, not a plain value
`console.log(data)` or `console.log(JSON.stringify(data))` logs the proxy object itself. The proxy still behaves like the real object for property access, but its printed representation looks different from a plain object.
:::

:::note Getting the raw value
Prior to version 0.44, you can read a variable's underlying value without going through the proxy via its `_data` property, for example `data._data` or `pageContext._data`. From version 0.44 onward, a hidden `test.getArguments(arguments)` function is available inside the executable to return the input variables unproxied.
:::
