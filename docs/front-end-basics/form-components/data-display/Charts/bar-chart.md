# Bar Chart

The Bar Chart component visualizes data using vertical bars, making it ideal for comparisons across categories or showing frequency distributions. It's an excellent option when you need a clear, comparative snapshot of numeric or categorical data.


<!-- ![image](images/chart/image-1.png)   -->

## Properties

The following properties are available to configure the behavior of the component from the form editor (this is in addition to [common properties](/docs/front-end-basics/form-components/common-component-properties)).

### Common

#### **Data Source Type** `object`  
Defines where the chart retrieves data from:
- Entity Type *(default)*
- URL

#### **Simple / Pivot** `object`  
Chart structure configuration:
- Simple *(default)*
- Pivot

#### **Axis Property** `object`  
Defines the property used on the X-axis.

#### **Entity Type** `object`  
Specifies the data entity from which values are drawn.

#### **Is Axis Property Time Series?** `boolean`  
Indicates whether the X-axis property represents a time series.

#### **Value Property** `object`  
Specifies the property whose values are to be visualized.

#### **Aggregation Method** `object`  
Determines how values are aggregated:
- Count *(default)*
- Sum
- Average
- Min
- Max

#### **Show X Axis** `boolean`  
Enables or disables display of the X-axis.

#### **Show X Axis Title** `boolean`  
Toggles the title for the X-axis.

#### **Show Y Axis** `boolean`  
Enables or disables display of the Y-axis.

#### **Show Y Axis Title** `boolean`  
Toggles the title for the Y-axis.

___

### Data
#### **Data Source Type** `object`  
Defines where the chart retrieves data from:
- Entity Type *(default)*
- URL

#### **Entity Type** `object`  
Specifies the data model or context.

#### **Axis Property** `object`  
Field used for the X-axis.

#### **Is Axis Property Time Series?** `boolean`  
Designates if X-axis values are dates or times.

#### **Value Property** `object`  
Specifies the metric or quantity being charted.

#### **Order By** `object`  
Determines sorting logic for chart data.

#### **Aggregation Method** `object`  
Defines how values are aggregated (same as above).

#### **Entity Filter** `object`  
Allows you to build complex filter conditions using a query builder interface. This tool helps narrow down the dataset displayed in the chart.

___

### Appearance

#### **Stroke Width** `number`  
Sets the width of the bar outlines.

#### **Stroke Color** `object`  
Defines the color of the stroke used for bars.

