---
sidebar_label: Icon Picker
title: Icon
---

# Icon Picker

The Icon Picker component lets you display and let users pick an icon from Shesha's icon library. Use it to represent actions, statuses, or categories visually, or to let a user choose an icon for something they're configuring (for example, an icon for a custom menu item).

![Image](./images/iconpicker1.png)

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Property name** `string`

The property this component reads and writes its selected icon value to.

#### **Label** `string`

The component's label text.

#### **Label Align** `string`

Aligns the label to the **left** or **right** of the icon.

#### **Icon Align** `object`

Aligns the icon itself within the component:

- **Left** (`start`) *(default)*
- **Center** (`center`)
- **Right** (`end`)

#### **Description** `string`

Additional descriptive text for the component.

___

### Color

Despite the panel's name, this group controls visibility and edit behaviour rather than color:

#### **Hidden** `boolean`

Hides the component when checked. Unlike the general Hide property, this is a plain on/off checkbox - it does not support switching to a JavaScript expression.

#### **Hide Label** `boolean`

Hides the Label without hiding the icon itself.

#### **Edit Mode** `object`

Controls whether the component is editable, read-only, or inherits its editability from the form, the same **Edit Mode** setting used across other components.

___

### Icon Styling

#### **Default Icon** `object`

Sets a default icon from the icon library, shown before the user (or a bound property) provides one.

#### **Color** `string`

Sets the icon's color.

#### **Border Width** `number`

The width of the border drawn around the icon.

#### **Border Color** `string`

The color of the icon's border.

#### **Border Radius** `number`

The corner radius of the icon's border, for rounded corners.

#### **Size** `number`

The icon's size in pixels. Defaults to `24`.

#### **Background Color** `string`

The background color behind the icon.

#### **Styling Box** `object`

Margin and padding around the component, using the same margin/padding format as the common properties, just labelled **Styling Box** here.
