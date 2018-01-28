# dc.js Canvas Scatter Plot
This is a drop-in replacement for the [dc.js](https://github.com/dc-js/dc.js) charting library that enables support for HTML5 Canvas based rendering of dc.js scatter plots while also retaining all SVG based charting options as in dc.js if the user wishes to utilize the SVG backend.

# Benefits of using a Canvas backend
While SVG based rendering enables easy generation of beautifully animated, data-driven charts and visualizations, managing thousands of SVG elements in the DOM can result in a lot of overhead. When plotting several thousand data points, SVG becomes a bottleneck and the user experience suffers greatly. This is particularly evident when using [dc.js scatter plots](https://dc-js.github.io/dc.js/docs/html/dc.scatterPlot.html) as typical use cases involve applying 2D brushes on these plots to interactively filter datasets. 

Canvas visualizations involve very little overhead since it is raster-based. Thus, canvas visualizations are typically only limited by the speed at which the client can process and pipe data for plotting to the canvas element.

# Examples

- [Canvas backend demo](https://www.intothevoid.io/wp-content/uploads/manual/2018/dcjs-scatter/scatter-canvas-large.html)

- [SVG backend demo](https://www.intothevoid.io/wp-content/uploads/manual/2018/dcjs-scatter/scatter-svg-large.html)

These examples are also included with this repo. Simply fire up your preferred local http server and then point your browser to the `localhost:serverport/examples/scatter-canvas-large.html` URL to view the demo showing 2 scatter plots, each rendering 20,000 data points on Canvas elements with fairly good responsiveness.

# Installation
Simply include the `dc-canvas-scatterplot.js` script file in your page/web-application **after** the dc.js library has been imported/required as this library simply binds to the dc namespace with a replacement `dc.scatterPlot` implementation.

# Usage
Using this library is fairly straightforward. Simply utilize `.useCanvas(true)` when initializing the scatterPlot element to ensure that the chart uses the Canvas backend for plotting. Otherwise, the `scatterPlot` interface is identical to that of the original dc.js library as [described in the dc.js docs](https://dc-js.github.io/dc.js/docs/html/dc.scatterPlot.html). Refer to the included examples for concrete examples showing how to create a scatter plot rendered to a Canvas element.

From the examples:
```javascript
var chart1 = dc.scatterPlot("#test1").width(300)
    .height(300)
    .width(350)
    .useCanvas(true) // Enable canvas backend for plot
    .x(d3.scale.linear().domain([-8, 8]))
    .yAxisLabel("Feature 2")
    .xAxisLabel("Feature 1")
    .keyAccessor(function (d) { return d.key[0]; })
    .valueAccessor(function (d) { return d.key[1]; })
    .clipPadding(10)
    .dimension(dim1)
    .highlightedSize(4)
    .symbolSize(3)
    .excludedSize(2)
    .excludedOpacity(0.5)
    .excludedColor('#ddd')
    .group(group1)
    .colorAccessor(function (d) { return d.key[2]; })
    .colors(function(colorKey) { return plotColorMap[colorKey]; });
```

# Missing features / Things you might want to know about
1. Currently, this library only supports circle symbol types for the Canvas backend. Since dc.js relies on d3 version 3, there is no easy way to create shape generators that can render out `d3.symbol` elements to a canvas backend. This is trivial in D3v4 but has not currently been implemented in order to keep the library lightweight and not force users to have dependencies on multiple versions of D3.

2. Canvas backend does not support any custom transition animations when hovering over legends or applying filters as is the case with the SVG backend. Transitions are more tricky and tedious when using Canvas, and since the primary use case of the canvas backend is when performance is critical, it seems reasonable to remove these aspects to the chart in order to make it feel more responsive and performant.

3. The library utilizes a hybrid SVG + Canvas approach to rendering the canvas charts. An SVG element is used to draw all axes and legends, etc. A canvas element is perfectly aligned and overlayed over the SVG element in order to plot the scatter points. In order to achieve this alignment of the SVG and Canvas elements, the following CSS properties are applied to the following elements **only when using the canvas backend**.

    a. The anchor div (i.e., the `parent` element supplied to `dc.scatterPlot`) is modified to have a CSS style of `position: relative`

    b. The SVG element is styled with `position: relative`

    c. The Canvas element is styled with `position: absolute; z-index: -1; pointer-events: none`. If the SVG element has any top/left properties, these are applied to the Canvas element in order to try and align it perfectly with the SVG element. 

    These CSS stylings may cause conflicts in more complex UIs though they are fairly non-intrusive. In such cases, modify your CSS in order to ensure correct alignment of the Canvas element with respect to the SVG element.

4. By default, all scatter plots will use the SVG backend for rendering the plots unless `useCanvas(true)` is passed in to the scatterPlot chart object during initialization.