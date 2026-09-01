---
sidebar_label: Slider 
title: Slider
---

# Slider

The Slider component lets users pick a numeric value by dragging a handle along a track, between a minimum and maximum bound. It binds to a numeric field on your entity.

![Image](../data-entry/images/slider1.png)

:::note Single value only
This component supports a single handle, for selecting one value between the minimum and maximum bound.
:::

---

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)). 

### Display

#### **Hidden** `boolean`

A plain on/off checkbox controlling whether the component is visible on the form. It is not scriptable.

#### **Hide Label** `boolean`

Hides the label text, showing only the slider.

___

### Data

#### **Default Value** `number`

The value the slider starts at when the form loads and no value has been saved yet.

#### **Minimum** `number`

The minimum allowable value on the slider.

#### **Maximum** `number`

The maximum allowable value on the slider.

___

### Style

#### **Style** `function`

A script that returns the style of the element as an object. The editor opens in a dialog rather than inline.
