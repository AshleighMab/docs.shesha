---
sidebar_label: Form instance API
title: Form instance API
---

# Form instance API

The `form` object gives your scripts access to the current form: its data, its mode, and a set of methods for updating fields, submitting, and showing loading feedback. It is available in any script alongside the other standard script variables.

---

## Reading Form State

#### **form.data** `object`

The form's current data. This is the same object as the top-level `data` variable.

**Form type to use:** Any form type.

**Example - Read the form's data:**

```javascript
const formData = form.data;
```

#### **form.formMode** `string`

The current form mode: `'designer'`, `'edit'`, or `'readonly'`.

#### **form.formSettings** `object`

The form's configurable settings, currently exposing `modelType` - the entity type the form is bound to.

#### **form.defaultApiEndpoints** `object`

The default CRUD endpoints Shesha generated for the form's bound entity: `create`, `read`, `update`, `delete`, and `list`. This is only populated when the form's Model Type is an existing entity - it is an empty object otherwise.

![Model binding](./images/model-binding.png)

**Example - Inspect the generated endpoints for a form bound to `Shesha.Domain.Person`:**

```javascript
console.log(form.defaultApiEndpoints);
```

```json
{
  "read": { "httpVerb": "GET", "url": "api/dynamic/Shesha/Person/Crud/Get" },
  "list": { "httpVerb": "GET", "url": "api/dynamic/Shesha/Person/Crud/GetAll" },
  "create": { "httpVerb": "POST", "url": "api/dynamic/Shesha/Person/Crud/Create" },
  "update": { "httpVerb": "PUT", "url": "api/dynamic/Shesha/Person/Crud/Update" },
  "delete": { "httpVerb": "DELETE", "url": "api/dynamic/Shesha/Person/Crud/Delete" }
}
```

#### **form.initialValues** `object`

The values the form had when it first loaded, before any user edits.

#### **form.parentFormValues** `object`

The field values of the parent form, if this form is a SubForm, modal, or other form nested inside another one.

#### **form.formArguments** `object`

The arguments passed to the form by whatever opened it (for example, query parameters or navigation arguments).

---

## Updating Form Data

#### **form.setFieldValue** `function`

Sets a single field's value, merging it into the form's existing data.

**Form type to use:** Edit Form or Create Form.

**Example - Set a single field:**

```javascript
form.setFieldValue('status', 1);
```

#### **form.setFieldsValue** `function`

Sets multiple field values at once, merging them into the form's existing data.

**Form type to use:** Edit Form or Create Form.

**Example - Set multiple fields:**

```javascript
form.setFieldsValue({ status: 1, reviewedBy: application.user.userName });
```

#### **form.clearFieldsValue** `function`

Clears all of the form's field values, replacing the data with an empty object rather than merging.

**Form type to use:** Edit Form or Create Form.

#### **form.addDelayedUpdateData** `function`

Attaches any pending delayed updates (updates deferred until the form is saved, such as attachments queued by a file upload component) to a data object under a `_delayedUpdate` key, and returns the list of delayed update groups.

```typescript
form.addDelayedUpdateData(data: object) => IDelayedUpdateGroup[]
```

#### **form.setFormData** `function`

:::warning Deprecated
This method is marked deprecated in the framework source. Use [`form.setFieldValue`](#formsetfieldvalue-function) or [`form.setFieldsValue`](#formsetfieldsvalue-function) instead.
:::

Sets the form's data directly from a `{ values, mergeValues }` payload.

---

## Submitting and Loading Feedback

#### **form.submit** `function`

Submits the form, the same as clicking its Submit button.

**Form type to use:** Edit Form or Create Form.

#### **form.showLoader** `function`

Shows a blocking loader overlay scoped to this form, and returns an instance you can use to update its message or close it.

**Form type to use:** Any form type.

**Example - Show a loader while an async action runs:**

```javascript
const loader = form.showLoader('Saving...');
await application.entities.shesha.Person.updateAsync({ input: { id: data.id, firstName: data.firstName } });
loader.close();
```

#### **form.hideLoaders** `function`

Hides all loaders currently shown for this form, including ones started elsewhere.

---

## Advanced

#### **form.formInstance** `object`

The underlying Ant Design `Form` instance that renders the form. See the [Ant Design Form documentation](https://ant.design/components/form) for its full API.

#### **form.shaForm** `object`

The internal Shesha form instance backing this form. This is an advanced, lower-level API - most scripts should use the methods above instead of reaching into `shaForm` directly.
