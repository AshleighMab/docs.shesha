---
sidebar_label: SetFormData
title: SetFormData
---

# SetFormData

`setFormData` is a script variable that updates the form's [data](/docs/front-end-basics/configured-views/client-side-scripting/shesha-objects/data) from inside a script, either merging new values in or replacing the data outright.

:::warning Deprecated
`setFormData` is marked `@deprecated`. Refer to [`form.setFieldsValue`](/docs/front-end-basics/javascript-api/form) to merge values, or `form.clearFieldsValue()` to reset the form's data, instead of calling `setFormData` directly. It still works on this version so you can recognise it in existing scripts, but new scripts should use the `form` API instead.
:::

---

## Signature

```typescript
setFormData: (payload: { values: object, mergeValues: boolean }) => void
```

- `values`: an object containing the data to set.
- `mergeValues`: `true` merges `values` into the form's existing data; `false` replaces the form's data entirely with `values`.

---

## Merge Behaviour

How `values` is applied depends on `mergeValues`, and the merge itself has a few non-obvious rules worth knowing before you rely on it:

- When `mergeValues` is `true`, `values` is deep-merged into the form's existing data - nested objects are merged property by property rather than the whole nested object being replaced.
- Arrays and date values inside `values` always replace the existing value outright, even while merging. They are never merged element by element.
- Setting a field to `null` or `undefined` inside `values` clears that field, even while merging - it is not treated as "no value provided".
- When `mergeValues` is `false`, the form's data is replaced entirely: any field not present in `values` is reset to its initial value rather than left as it was. `form.clearFieldsValue()` is implemented as exactly this - `setFormData({ values: {}, mergeValues: false })`.
- Calling `setFormData({ values: {}, mergeValues: true })` - an empty `values` object with merging on - is a no-op. It returns immediately without updating the form's data or firing change events.

---

## Examples

**Form type to use:** Any form type - `setFormData` is available anywhere standard script variables are.

**Example - Set a single value:**

```javascript
setFormData({
  values: {
    emailAddress: "admin@shesha.io",
  },
  mergeValues: true,
});
```

**Example - Set multiple values at once:**

```javascript
setFormData({
  values: {
    emailAddress: "admin@shesha.io",
    name: "Shesha",
  },
  mergeValues: true,
});
```

**Example - Set a nested value:**

```javascript
setFormData({
  values: {
    organisation: {
      emailAddress: "admin@shesha.io",
      name: "Shesha",
    },
  },
  mergeValues: true,
});
```

---

## See Also

- [Form Data](/docs/front-end-basics/configured-views/client-side-scripting/shesha-objects/data)
- [Form instance API](/docs/front-end-basics/javascript-api/form)
