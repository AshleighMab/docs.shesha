---
sidebar_label: App Context
title: App Context
---

# App Context

App Context is a storage object shared across the whole application session. Components can be bound to it directly, or a script can read and write it, so it's a convenient way to share a value between otherwise unrelated forms and pages for as long as the session lasts.

:::info
Contexts let you keep several separate stores of data and layer extra configuration on top of a form. For example, you could use a checkbox bound to App Context to toggle whether certain fields elsewhere on the page are required or visible, or use the length of some text to decide whether to show a warning.
:::

## Data Entry

Any component with a [Property Name](/docs/front-end-basics/form-components/common-component-properties#property-name-string) can be pointed at App Context instead of the form's own data, by setting its [Context](/docs/front-end-basics/form-components/common-component-properties#context-object) property to `appContext`.

![Data Entry](./images/context2.png)

For example, a Checkbox component with Property Name `check` and Context set to `appContext` writes its value onto App Context under that name, so `contexts.appContext` looks like this:

```json
{
  "check": true
}
```

You can read that value from a script the same way you'd read any other variable:

```javascript
contexts.appContext.check
```

:::note
This page covers binding a component's Context to App Context from the form designer. For the full scripting API, including how to write to App Context from a script (`setFieldValue`) and how it differs from Page Context and Form Context, see [App Context](/docs/front-end-basics/javascript-api/contexts/appContext).
:::
