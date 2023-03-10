# 使用 React 拖动 SVG

> 原文：<https://dev.to/tvanantwerp/dragging-svgs-with-react-38h6>

我最近开始使用一个新版本的映射工具，它可以让人们从 CSV 文件中创建 [choropleths](https://en.wikipedia.org/wiki/Choropleth_map) 。该工具结合了 React 和一些 D3 库来创建美国的 SVG 地图，包括数据标签。

[![US choropleth showing tobacco tax rates.](img/e8194a08711273e33a1ff93255470055.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9GQy1UMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aqni6k1gw20wskvv1d5r.png)

默认情况下，这些标签被放置在州的`path`形状的[质心](https://en.wikipedia.org/wiki/Centroid)处，并为一些更奇怪的州边界手动指定一些偏移量。但是，即使使用手动偏移，这些生成的标签仍然可能定位不佳。我希望能够点击并拖动这些标签到一个更好的位置。

[![Badly placed labels.](img/f8c7c15a27ed3fae65db1d6132d57c3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hVeDa2f8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n0o0zbsucxqkqomwhmtc.png)

SVG 并不总是兼容的。他们没有实现[拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) ，所以对我们来说没有`ondrag`事件。虽然我在这个项目中使用了 D3 库，但它主要只是用于解析 topojson 和创建美国州 T2，而不是创建 SVG 元素或管理数据。React 处理编程状态和组件创建/修改。这意味着`d3-drag`，它似乎与 D3 在 DOM 中创建/修改 SVG 元素的范例紧密相关，硬塞进去可能会很痛苦。

所以，让我们手动实现拖放！

首先，这里是基本的标签组件。只是一个`g`元素中的两个`text`元素将被添加到 SVG 中。该组件还有一个样式`user-select: none`,用来防止选择文本而不是拖动文本。

```
const Label = ({center, adjustment, name, value}) => {
  // Use the centroid coordinates and manual adjustments
  // from props to set X and Y of label in the SVG
  const labelX = center[0] + adjustment[0];
  const labelY = center[1] + adjustment[1];

  return (
    <g style={{userSelect: 'none'}} transform={`translate(${labelX}, ${labelY})`}>
      <text>
        {name}
      </text>
      <text>
        {value}
      </text>
    </g>
  );
}; 
```

虽然 SVG 可能没有实现拖放 API，但是我们仍然可以使用鼠标事件！我们将使用`mousedown`事件来知道我们正在尝试拖动，使用`mousemove`事件来决定我们已经拖动了多远并相应地更新位置，使用`mouseup`事件来知道我们已经完成了拖动。

```
const Label = ({center, adjustment}) => {
  const [dragging, setDragging] = useState(false);

  const labelX = center[0] + adjustment[0];
  const labelY = center[1] + adjustment[1];

  return (
    <g
      style={{userSelect: 'none'}} 
      transform={`translate(${labelX}, ${labelY})`}
      onMouseDown={e => {
        // We have clicked the label, starting the drag.
        setDragging(true);
      }}
      onMouseMove={e => {
        // As long as we haven't let go of the mouse button,
        // we are still dragging.
        if (dragging) {
          // Drag behavior will go here.
        }
      }}
      onMouseUp={() => {
        // We let go of the mouse, ending our drag.
        setDragging(false);
      }}
    >
      ...
    </g>
  );
}; 
```

我们的标签组件现在知道它是否被拖动。为了真正地重新定位组件，我们需要跟踪我们开始的坐标以及我们已经远离它们多远了。

```
const Label = ({center, adjustment}) => {
  const [dragging, setDragging] = useState(false);
  const [coordinates, setCoordinates] = useState({ x: 0, y: 0 });
  const [origin, setOrigin] = useState({ x: 0, y: 0 });

  // Add our new coordinates to the X and Y position values.
  const labelX = center[0] + adjustment[0] + coordinates.x;
  const labelY = center[1] + adjustment[1] + coordinates.y;

  return (
    <g
      style={{userSelect: 'none'}} 
      transform={`translate(${labelX}, ${labelY})`}
      onMouseDown={e => {
        // Record our starting point.
        setOrigin({ x: e.clientX, y: e.clientY });
        setDragging(true);
      }}
      onMouseMove={e => {
        if (dragging) {
          // Set state for the change in coordinates.
          setCoordinates({
            x: e.clientX - origin.x,
            y: e.clientY - origin.y,
          });
        }
      }}
      onMouseUp={() => {
        setDragging(false);
      }}
    >
      ...
    </g>
  );
}; 
```

就是这样！我们现在在 SVG 中有了一个可拖动的标签，而且不需要任何库来完成。

[![Drag those labels!](img/82a7768d29a99af848c07994aa1ae91b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zNbMqgKj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjamo6qwmzobqq6n0lfn.gif)