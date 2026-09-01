---
sidebar_label: Autocomplete
title: Autocomplete
---

# Autocomplete

The Autocomplete component is a search-as-you-type input that looks up matching records or values while the user types, instead of asking them to pick from a long static list. It can search a Shesha entity, or a custom URL endpoint you provide.

![Image](../Advanced/images/autocomplete1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Selection Mode** `string`

Whether the user can select a single item (`single`, the default) or multiple items (`multiple`).

#### **Disable Search** `boolean`

Hides the search box and disables typing-based filtering, leaving only a plain dropdown of items.

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It has no JavaScript-expression mode.

___

### Data

#### **Default Value** `function`

A script that returns the component's default value. Has access to the full standard set of script variables (`data`, `form`, `formMode`, `globalState`, `http`, `message`, `moment`, `setFormData`, `setGlobalState`) plus the row currently selected in a Data Table context, if any.

#### **Data Source type** `string`

Where the component fetches its suggestions from:

| Option | Description |
|---|---|
| **Entities List** | Searches a Shesha entity via the standard entities API |
| **Url** | Searches a custom endpoint you provide |

The next four properties are shown only when Data Source type is **Url**.

#### **Data Source Url** `string`

The endpoint to call - it must accept a `term` query parameter for the typed search text and return items shaped `{ value, displayText }`.

#### **Key Prop Name** `string`

Lets you use a different response shape by naming the field to read as each item's key (`value`) instead of the default.

#### **Value Prop Name** `string`

Lets you use a different response shape by naming the field to read as each item's label (`displayText`) instead of the default.

#### **Query Param** `object`

Adds extra fixed or templated query string parameters to every request.

___

The next three properties are shown only when Data Source type is **Entities List**.

#### **Entity Type** `string`

Picks which entity to search.

#### **Display Property** `string`

Names the field used as each option's display text. Leave empty to use the entity's default display name.

#### **Entity Filter** `object`

A query-builder filter that narrows the search to a subset of records. It can't be configured until **Entity Type** is set.

#### **Use raw values** `boolean`

Controls how the selected item is stored on the form's data:

| Use raw values | Stored value |
|---|---|
| Off (default) | An entity reference object: `{ id, _className, _displayName }` |
| On | The raw key value only (for example, just the ID string) |

#### **Allow Free Text** `boolean`

Shown only when Data Source type is **Url** and **Use raw values** is on. Lets the user submit typed text that doesn't match any suggestion, instead of being restricted to items returned by the endpoint.

<LayoutBanners url="https://app.guideflow.com/embed/6kw11ndfzp" type={1}/>

<LayoutBanners url="https://app.guideflow.com/embed/ok8eev2fxk" type={1}/>

___

### Events

#### **On Change** `function`

Fires when the user selects an option or types a value. Alongside the standard script variables, it also exposes `value` (the component's new value) and `option` (the metadata of the selected item).

___

### Validation

Only contains the standard **Required** field.

___

### Quickview

#### **Use Quickview** `boolean`

When enabled, clicking a selected item opens it in a read-only Quickview dialog instead of just showing its label. Enabling this reveals four more fields:

| Field | What it controls |
|---|---|
| **Form Path** | The form used to render the Quickview dialog's content |
| **Get Entity Url** | The endpoint used to fetch the full entity for display |
| **Display Property Name** | The property shown as the Quickview's title |
| **Width** | The width of the Quickview dialog |

___

### Style

The Style panel includes Size, Height, Width, a Hide Border toggle (with Border Width, Radius, Type, and Color), Background Color, Margin & Padding, and a Style script.

___

### Security

Only contains the standard **Permissions** field.

---

## Response Formats

Regardless of Data Source type, custom URL endpoints must accept a `term` query parameter for filtering, and return either a standard Shesha list response or a plain array:

```ts
export interface ITableDataResponse {
    readonly totalCount: number;
    readonly items: object[];
}
```

### Entities List

If the standard entities endpoint is used, the backend returns a list of entities shaped like:

```js
{
    "id": "d519b92f-86e9-4f0f-8df4-00aae8a43158",
    "_className": "Shesha.Domain.Person",
    "_displayName": "Alex Stephens"
}
```

If you set **Display Property** (for example `firstName`), the response includes that extra field so it can be used as the item's display text:

```js
{
    "id": "d519b92f-86e9-4f0f-8df4-00aae8a43158",
    "_className": "Shesha.Domain.Person",
    "_displayName": "Alex Stephens",
    "firstName": "Alex"
}
```

With **Use raw values** off (the default), the selected value is stored as an entity reference:

```js
{
    "autocomplete": {
        "id": "d519b92f-86e9-4f0f-8df4-00aae8a43158",
        "_className": "Shesha.Domain.Person",
        "_displayName": "Alex Stephens"
    }
}
```

### Url

The standard response shape from a custom endpoint is:

```js
// Received item
{
    "value": 1,
    "displayText": "First option"
}

// Selected value (Use raw values on)
{
    "autocomplete": 1
}
```

You can use a source with a different response shape by setting **Key Prop Name** and **Value Prop Name** to the fields that should be read as the value and label instead.
