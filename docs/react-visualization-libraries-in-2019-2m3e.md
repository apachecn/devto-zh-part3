# 2019 年的 React 可视化库

> 原文：<https://dev.to/cubejs/react-visualization-libraries-in-2019-2m3e>

在处理 [Cube.js](https://github.com/cube-js/cube.js) 时，我们看到了许多不同的可视化库。鉴于 Cube.js 在 SQL 数据库之上提供了一个用于分析的 API 层，并且不涉及可视化领域，任何图表库都可以使用它来构建仪表板。这就是为什么我们总是在寻找一个好的和开发人员友好的可视化库。

下面的列表是针对 React 特定的库的。我将尝试构建几乎相同的堆叠条形图，其中包含轴格式、图例和工具提示。对于数据后端，我们将使用 Heroku 上托管的 Cube.js。我还将使用 [Cube.js React 绑定](https://cube.dev/docs/@cubejs-client-react)，它使用[渲染道具](https://reactjs.org/docs/render-props.html)技术，本身不渲染任何东西，让我们用任何图表库构建我们想要的任何东西。

同时，我还将使用两个非常方便的库— `moment.js`和`numeral.js`，分别用于日期和数字的格式化。

作为数据输入，我们将使用来自 Cube.js Javascript 客户端的 [resultSet.chartPivot()](https://cube.dev/docs/@cubejs-client-core#result-set-chart-pivot) 方法。它返回一个数据数组，在我们的例子中，它看起来像这样:

```
[
  {
    "x": "2017-01-01T00:00:00.000",
    "completed, Orders.count": 208,
    "processing, Orders.count": 252,
    "shipped, Orders.count": 233
  },
  {
    "x": "2017-02-01T00:00:00.000",
    "completed, Orders.count": 188,
    "processing, Orders.count": 209,
    "shipped, Orders.count": 222
  },
  …
] 
```

现在，让我们跳到列表。

*如果没有看到你最喜欢的图书馆或者只是想让我再添加一个——只需在[这个公共 Slack 社区](https://cubejs-community.herokuapp.com/)中 ping 我。我很高兴尽我所能将更多的库添加到列表中。*

## 充值

[Recharts](http://recharts.org) 提供了一套模块化的图表组件，通过让你将这些组件混合在一起，可以轻松地构建像[组成的线条和条形图](http://recharts.org/en-US/examples/LineBarAreaComposedChart)这样的东西。

这是迄今为止最受欢迎的图书馆。它在 Github 上有超过 11k 颗星星，但也有大量(迄今为止有 600 颗)未解决的问题。

文档相当广泛，但在某些部分缺乏细节。你可以在 [Rechart 的网站](http://recharts.org)上找到一堆例子，这可能是构建你自己的图表的一个很好的起点。

Recharts 有很多定制选项。除此之外，它允许通过定制 SVG 元素进行低级定制。

下面是如何在 Recharts 中构建我们的堆积条形图。

*在这个例子和接下来的例子中，我使用了`colors`、`dateFormatter`和`numberFormatter`变量。下面是它们的定义:*

```
const numberFormatter = item => numeral(item).format("0,0");
const dateFormatter = item => moment(item).format("MMM YY");
const colors = ["#7DB3FF", "#49457B", "#FF7C78"]; 
```

```
export default ({ resultSet, colors, dateFormatter, numberFormatter }) => (
  <ResponsiveContainer width="100%" height={300}>
    <BarChart data={resultSet.chartPivot()}>
      <XAxis tickFormatter={dateFormatter} dataKey="x" />
      <YAxis tickFormatter={numberFormatter} />
      {resultSet.seriesNames().map((series, i) => (
        <Bar
          stackId="a"
          dataKey={series}
          name={series.split(",")[0]}
          fill={colors[i]}
        />
      ))}
      <Legend />
      <Tooltip labelFormatter={dateFormatter} formatter={numberFormatter} />
    </BarChart>
  </ResponsiveContainer>
); 
```

[https://codesandbox.io/embed/4j4jlz33z0?initialpath=/recharts](https://codesandbox.io/embed/4j4jlz33z0?initialpath=/recharts)

## 胜利

胜利遵循着与胜利相同的可组合模式。它是由[强大的](https://formidable.com)开发的，除了 Victory 之外，它还有其他可靠的开源库。这是列表中唯一一个可以和 React Native 一起工作的库。

它是继 Recharts 之后第二受欢迎的图书馆，在 Github 上有超过 7K 的星星，但开放问题比 Recharts 少 5 倍。胜利在 Spectrum 上有一个[社区聊天。](https://spectrum.chat/victory)

它被很好地记录了下来，并有一个 T2 的样本库。

Victory 有两个主题:灰度和材料，并允许您创建客户主题，以便在所有图表中保持一致的外观。

下面你可以看到我们的胜利堆积条形图的代码:

```
const transformResult = (series, resultSet) =>
  resultSet.chartPivot().map(element => ({ x: element.x, y: element[series] }));

export default ({ resultSet, dateFormatter, colors, numberFormatter }) => (
  <div height={300}>
    <VictoryChart
      containerComponent={
        <VictoryVoronoiContainer
          voronoiDimension="x"
          labels={(d, i) => `${resultSet.seriesNames()[i]}: ${d.y}`}
          labelComponent={
            <VictoryTooltip cornerRadius={0} flyoutStyle={{ fill: "white" }} />
          }
        />
      }
      domainPadding={{ x: 20, y: [0, 20] }}
    >
      <VictoryLegend
        colorScale={colors}
        data={resultSet.seriesNames().map(series => ({
          name: series.split(",")[0]
        }))}
        orientation="horizontal"
        y={275}
        x={130}
      />
      <VictoryAxis tickFormat={dateFormatter} tickCount={8} />
      <VictoryAxis dependentAxis />
      <VictoryStack colorScale={colors}>
        {resultSet.seriesNames().map((series, i) => (
          <VictoryBar key={i} data={transformResult(series, resultSet)} />
        ))}
      </VictoryStack>
    </VictoryChart>
  </div>
); 
```

[https://codesandbox.io/embed/4j4jlz33z0?initialpath=/victory](https://codesandbox.io/embed/4j4jlz33z0?initialpath=/victory)

## Nivo

除了 Recharts 和 Victory 之外， [Nivo](https://nivo.rocks/) 也是建立在 D3 之上的，并且是 React 特有的。但是与以前的库不同，它是不可组合的。它为每种图表类型提供了一个组件，该组件通过多个 props 进行配置。Nivo 是作为一组特定图表类型的包分发的，例如，`@nivo/bar`。因此，需要安装特定的包来使用特定的图表类型。

项目本身相当活跃；它在 Github 上有超过 5000 名明星，在[社区聊天](https://discordapp.com/invite/n7Ft74f)中有近 300 名成员。

它有交互式文档，您可以在其中为您的图表构建配置。虽然它很好，但是缺少一个好的旧的文本 API 参考和搜索选项。它还有一本[故事书，里面有例子](https://nivo.rocks/storybook/)。它帮助我缩短了构建第一个图表的时间。与 Victory 相同，Nivo 允许您创建自己的主题，以便在所有图表中保持一致的外观。

我们将为堆栈条形图使用`@nivo/bar`包；您可以在下面找到一个代码示例和 CodeSandbox 演示。

```
//https://github.com/plouc/nivo/issues/138#issuecomment-373015114
const ticksFormmater = (ticksCount, value, data, dateFormatter) => {
  const valueIndex = data.map(i => i.x).indexOf(value);
  if (valueIndex % Math.floor(data.length / ticksCount) === 0) {
    return dateFormatter(value);
  }

  return "";
};

export default ({ resultSet, colors, dateFormatter, numberFormatter }) => (
  <div style={{ height: 300 }}>
    <ResponsiveBar
      enableLabel={false}
      colors={colors}
      data={resultSet.chartPivot()}
      keys={resultSet.seriesNames()}
      indexBy="x"
      enableGridY={false}
      padding={0.3}
      margin={{ top: 60, right: 80, bottom: 60, left: 40 }}
      axisLeft={{
        format: numberFormatter
      }}
      axisBottom={{
        format: value =>
          ticksFormmater(8, value, resultSet.chartPivot(), dateFormatter)
      }}
      tooltip={({ id, value, color }) => (
        <strong style={{ color }}>
          {id.split(",")[0]}: {numberFormatter(value)}
        </strong>
      )}
      legends={[
        {
          anchor: "bottom",
          direction: "row",
          translateY: 50,
          itemsSpacing: 2,
          itemWidth: 150,
          itemHeight: 20,
          itemDirection: "left-to-right"
        }
      ]}
    />
  </div>
); 
```

[https://codesandbox.io/embed/4j4jlz33z0?initialpath=/nivo](https://codesandbox.io/embed/4j4jlz33z0?initialpath=/nivo)

## 商业图表

[BizCharts](https://bizcharts.net/products/bizCharts) 是基于可视化语法 [G2](https://github.com/antvis/g2) 的 React 专用可视化库。它得到了阿里巴巴的支持，与蚂蚁设计框架配合得很好。

它在 Github 上有近 4k 颗星，但大部分开发是中文的，大部分文档也是如此。我认为团队在翻译文档方面做得很好，但这仍是一项进行中的工作。

虽然它缺少英文文档，但 API 非常简单。唯一困难的是将数据重新格式化为堆积图的特定格式。

```
const stackedChartData = resultSet => {
  const data = resultSet
    .pivot()
    .map(({ xValues, yValuesArray }) =>
      yValuesArray.map(([yValues, m]) => ({
        x: resultSet.axisValuesString(xValues, ", "),
        color: resultSet.axisValuesString(yValues, ", "),
        measure: m && Number.parseFloat(m)
      }))
    )
    .reduce((a, b) => a.concat(b));

  return data;
};

export default ({ resultSet, dateFormatter, colors, numberFormatter }) => (
      <Chart
        scale={{ x: { tickCount: 10 } }}
        height={400}
        data={stackedChartData(resultSet)}
        forceFit
      >
        <Axis name="x" label={{ formatter: dateFormatter }} />
        <Axis label={{ formatter: numberFormatter }} name="measure" />
        <Tooltip />
        <Geom
          type="intervalStack"
          position={`x*measure`}
          color={["color", colors]}
        />
        <Legend itemFormatter={item => item.split(",")[0]} />
      </Chart>
    )}
  /> ); 
```

[https://codesandbox.io/embed/4j4jlz33z0?initialpath=/bizcharts](https://codesandbox.io/embed/4j4jlz33z0?initialpath=/bizcharts)

## 反应-可见

React-vis 由优步开发，看起来相当活跃，有 5.4k Github stars，150 个公开问题，以及许多最近的提交。它是模块化的，就像列表中的大多数库一样。它附带了一些不错的默认样式，应该作为 CSS 文件单独导入。

和 Nivo 一样，它有带例子的故事书。组件 API 也有很好的文档记录。在定制方面，您可以通过定制 CSS 样式来控制外观。React-vis 还提供了构建定制 SVG 图表的特定组件— [CustomSVGSeries](https://uber.github.io/react-vis/documentation/series-reference/custom-svg-series) 。

API 类似于 Victory 的。你可以从 Victory 的片段中看到我使用了几乎相同的`transformResult`。

```
const transformResult = (series, resultSet, dateFormatter) =>
  resultSet
    .chartPivot()
    .map(element => ({ x: Date.parse(element.x), y: element[series] }));

export default ({ resultSet, dateFormatter, colors, numberFormatter }) => (
  <XYPlot xType="time" height={300} width={500} stackBy="y">
    <XAxis tickFormat={dateFormatter} tickSize={8} />
    <YAxis />
    {resultSet.seriesNames().map((series, i) => (
      <VerticalBarSeries
        cluster="stack 1"
        key={i}
        color={colors[i]}
        data={transformResult(series, resultSet, dateFormatter)}
      />
    ))}
    <DiscreteColorLegend
      colors={colors}
      items={resultSet.seriesNames().map(i => i.split(",")[0])}
      orientation="horizontal"
      style={{ position: "absolute", left: 130, bottom: -30 }}
    />
  </XYPlot>
); 
```

[https://codesandbox.io/embed/4j4jlz33z0?initialpath=/react-vis](https://codesandbox.io/embed/4j4jlz33z0?initialpath=/react-vis)

*如果没有看到你最喜欢的图书馆或者只是想让我再添加一个——只需在[这个公共 Slack 社区](https://cubejs-community.herokuapp.com/)中 ping 我。我很高兴尽我所能将更多的库添加到列表中。*