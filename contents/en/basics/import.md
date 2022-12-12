# Use Apache ECharts with bundler and NPM

If your development environment uses a package management tool like `npm` or `yarn` and builds with a packaging tool like Webpack, this article will describe how to get a minimal bundle of Apache ECharts<sup>TM</sup> via treeshaking.

## Install ECharts via NPM

You can install ECharts via npm using the following command

```shell
npm install echarts --save
```

## Import ECharts

```js
import * as echarts from 'echarts';

// initialize the echarts instance
var myChart = echarts.init(document.getElementById('main'));
// Draw the chart
myChart.setOption({
  title: {
    text: 'ECharts Getting Started Example'
  },
  tooltip: {},
  xAxis: {
    data: ['shirt', 'cardigan', 'chiffon', 'pants', 'heels', 'socks']
  },
  yAxis: {},
  series: [
    {
      name: 'sales',
      type: 'bar',
      data: [5, 20, 36, 10, 10, 20]
    }
  ]
});
```

## Importing Required Charts and Components to Have Minimal Bundle

The above code will import all the charts and components in ECharts, but if you don't want to bring in all the components, you can use the tree-shakeable interface provided by ECharts to bundle the required components and get a minimal bundle.

```js
// Import the echarts core module, which provides the necessary interfaces for using echarts.
import * as echarts from 'echarts/core';
// Import bar charts, all suffixed with Chart
import { BarChart } from 'echarts/charts';
// Import the tooltip, title, rectangular coordinate system, dataset and transform components
// all suffixed with Component
import {
  TitleComponent,
  TooltipComponent,
  GridComponent,
  DatasetComponent,
  TransformComponent
} from 'echarts/components';
// Features like Universal Transition and Label Layout
import { LabelLayout, UniversalTransition } from 'echarts/features';
// Import the Canvas renderer
// Note that including the CanvasRenderer or SVGRenderer is a required step
import { CanvasRenderer } from 'echarts/renderers';

// Register the required components
echarts.use([
  TitleComponent,
  TooltipComponent,
  GridComponent,
  DatasetComponent,
  TransformComponent,
  BarChart,
  LabelLayout,
  UniversalTransition,
  CanvasRenderer
]);

// The next step is the same as before, initialize the chart and set the configuration items
var myChart = echarts.init(document.getElementById('main'));
myChart.setOption({
  // ...
});
```

> Note that in order to keep the size of the package to a minimum, ECharts does not provide any renderer in tree-shakeable interface, so you need to choose to import `CanvasRenderer` or `SVGRenderer` as the renderer. The advantage of this is that if you only need to use the svg rendering mode, the bundle will not include the `CanvasRenderer` module, which is not needed.

The "Full Code" tab on our sample editor page provides a very convenient way to generate a tree-shakable code. It will generate tree-shakable code based on the current option dynamically. You can use it directly in your project.

## Minimal Option Type in TypeScript

For developers who are using TypeScript to develop ECharts, we provide a type interface to combine the minimal `EChartsOption` type. This stricter type can effectively help you check for missing components or charts.

```ts
import * as echarts from 'echarts/core';
import {
  BarChart,
  // The series types are defined with the SeriesOption suffix
  BarSeriesOption,
  LineChart,
  LineSeriesOption
} from 'echarts/charts';
import {
  TitleComponent,
  // The component types are defined with the suffix ComponentOption
  TitleComponentOption,
  TooltipComponent,
  TooltipComponentOption,
  GridComponent,
  GridComponentOption,
  // Dataset
  DatasetComponent,
  DatasetComponentOption,
  // Built-in transform (filter, sort)
  TransformComponent
} from 'echarts/components';
import { LabelLayout, UniversalTransition } from 'echarts/features';
import { CanvasRenderer } from 'echarts/renderers';

// Combine an Option type with only required components and charts via ComposeOption
type ECOption = echarts.ComposeOption<
  | BarSeriesOption
  | LineSeriesOption
  | TitleComponentOption
  | TooltipComponentOption
  | GridComponentOption
  | DatasetComponentOption
>;

// Register the required components
echarts.use([
  TitleComponent,
  TooltipComponent,
  GridComponent,
  DatasetComponent,
  TransformComponent,
  BarChart,
  LineChart,
  LabelLayout,
  UniversalTransition,
  CanvasRenderer
]);

const option: ECOption = {
  // ...
};
```
