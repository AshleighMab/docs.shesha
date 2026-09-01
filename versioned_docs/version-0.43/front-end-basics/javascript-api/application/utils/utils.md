---
sidebar_label: Utils
title: Utils
---

# Utils

The `application.utils` object gives your form scripts a way to build strings from a template and some data, without writing your own string-replacement logic. It currently exposes a single function, `evaluateString`.

---

## application.utils.evaluateString

Evaluates a template string written in [Mustache syntax](https://mustache.github.io/) against a data object, replacing each `{{ }}` placeholder with the matching value from that object.

```typescript
application.utils.evaluateString(template: string, data: any): string
```

**Form type to use:** Any form type - `application` is available everywhere standard script variables are.

**Example - Build a summary line from record data:**

```javascript
const data = {
  id: 'REC-1001',
  name: 'Shesha',
  status: 'Active',
};

const template = 'Record {{id}} belongs to {{name}} and is currently {{status}}.';

const result = application.utils.evaluateString(template, data);

console.log(result);

// result: "Record REC-1001 belongs to Shesha and is currently Active."
```

:::tip Formatting dates
The data object passed in automatically gets a `dateFormat` helper you can wrap around a date value, so you don't have to format it yourself first:

```javascript
const template = 'Created on {{#dateFormat}}{{createdDate}}{{/dateFormat}}.';
```
:::
