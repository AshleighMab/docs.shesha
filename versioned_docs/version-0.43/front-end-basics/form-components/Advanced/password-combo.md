---
sidebar_label: Password Combo
title: Password Combo
---

# Password Combo

The Password Combo component pairs a password field with its confirmation field in a single component. While the user types, a popover shows a live checklist of the password rules so they can see exactly what's missing, and the confirmation field checks that it matches the password before the form can be submitted.

![Image](./images/passwordcombo1.png)

---

## Properties

### Display

#### **Label** `string` / **Label: Confirmation** `string`

Separate label text for the password field and the confirmation field.

#### **Label Align** `object`

Aligns both labels left or right.

#### **Placeholder** `string` / **Placeholder: Confirmation** `string`

Separate placeholder text for the password field and the confirmation field.

#### **Description** `string` / **Description: Confirmation** `string`

Separate tooltip text for the password field and the confirmation field.

#### **Hidden** `boolean`

A plain on/off checkbox that hides the component - not a scriptable expression.

#### **Hide Label** `boolean`

Hides both labels without hiding the fields themselves.

___

### Validation

#### **Min Password Length** `number`

The minimum number of characters the password must contain. Defaults to `4`. This also drives the wording of the length rule shown in the checklist popover (for example, "be four characters or longer").

#### **Message** `string`

A custom error message shown when the password fails validation, replacing the default message.

#### **Validator** `function`

A script for custom validation logic, returning a Promise. Exposed variables: `rules` (the validate rules for this field), `value` (the current value), `callback` (called by the component), and `data` (the form's values).

:::note The checklist rules are fixed
The popover checklist always checks for a lowercase letter, an uppercase letter, a numeric character, a special character, and the minimum length - these four character-variety rules are not individually configurable, only the minimum length is (via **Min Password Length**). This checklist is separate from the application-wide Password Complexity settings on the Settings page - the two are not linked.
:::

___

### Style


#### **Hide Border** `boolean`

Removes the input's border.
