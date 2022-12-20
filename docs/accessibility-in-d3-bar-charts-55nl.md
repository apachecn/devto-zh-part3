# d3 条形图中的可访问性

> 原文：<https://dev.to/lkopacz/accessibility-in-d3-bar-charts-55nl>

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/7119149108/de227e02a8de9baf9ee0672bae9a9f27a2c7485f.3477c826-1e13-4084-9266-6af9016a1c57.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/7119149108/de227e02a8de9baf9ee0672bae9a9f27a2c7485f.3477c826-1e13-4084-9266-6af9016a1c57.mp3)

*最初发布于[www.a11ywithlindsey.com](https://www.a11ywithlindsey.com/blog/accessibility-d3-bar-charts)。*

嘿你们好。首先，我想感谢人们对这篇文章的耐心。在我发表最后一篇文章后的几个星期里，我感到非常紧张。当然，我选择在紧张的工作期限中间做一个技术性很强的岗位。截止日期已经过了，我终于完成了这篇文章！

我在推特上问你希望我接下来写什么。许多人要求我谈谈数据可视化的可访问性。因为我学到了太多关于数据可视化的知识，所以我决定将这个系列作为文章。

在我的系列文章的第一部分，我将和你谈谈无障碍条形图！

## 首发出场

我将使用 d3 JavaScript 库，因为我对它很熟悉。我正在构建一个内联 SVG，我们讨论的原则也适用于 SVG。关于 SVG 的伟大之处在于，如果您知道自己在做什么，制作可访问的条形图就非常简单。然而，有一点，知道你在做什么！

下面是我正在使用的数据集:

```
const data = [
  {
    name: 'Banana Production',
    value: 20223290811,
  },
  {
    name: 'Apple Production',
    value: 8191091088.532,
  },
  {
    name: 'Clementine Production',
    value: 1162341399.19,
  },
] 
```

我看到的大多数条形图的问题是缺少指示数据是什么的文本元素。他们只有带轴的可视条来指示它的值。

[https://codepen.io/littlekope0903/embed/BEMNGO/?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/BEMNGO/?height=600&default-tab=result&embed-version=2)

那么这有什么问题呢？我们有一些条形和轴标签的文本。然而，屏幕阅读器不会反映与数据集相关联的**值**。

使用 VoiceOver 命令时，它只读取标签，然后读取轴刻度。我想读的是标签，然后是数据值。

我们想要的是有视觉意义的`<rect>`元素旁边的`<text>`元素。对于屏幕阅读器来说，最好的事情就是确保有**可读的内容**。内联 SVG 对于可访问性非常好，因为图像变成了标记。太棒了。但是，如果您的条形图只与形状交流数据，默认情况下屏幕阅读器不会读取它。

## 潜在解决方案#1

使我的条形图可访问的第一个解决方案是在我调用了`xAxis`之后添加一个文本元素。

```
d3.selectAll('.tick')
  .append('text')
  .text((d, i) =>
    d3
      .format('.2s')(data[i].value)
      .replace('G', 'B')
  ) 
```

默认情况下，`.tick`类是 d3-axis 附带的，它附加到附带的`<g>`元素上。我选择了所有的`.tick`元素，并附加了一个格式化的文本元素。

[https://codepen.io/littlekope0903/embed/vMwPdx/?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/vMwPdx/?height=600&default-tab=result&embed-version=2)

虽然这适用于屏幕阅读器，但我不认为这是每个人最容易获得的数据可视化体验。yAxis 的巨大范围可能会使理解其对视觉用户的价值具有挑战性。因为 y 轴指向超过 160 亿，所以可以有把握地假设，对于我们的用户来说，数据的价值可能并不明显。如果 y 轴范围是 0 - 10，可能会有所不同。

拥有`<text>`元素对屏幕阅读器用户来说是更好的体验，但是我们可以为视力正常的用户改进它。

## 潜在方案二

另一个解决方案是包含一个图例。重要的是要注意，对于色盲来说，有一个彩色编码的条形图可能不是最容易使用的。如果我们走这条路，我们必须确保每个酒吧之间的强烈和可及的对比。

[https://codepen.io/littlekope0903/embed/WWBmWY/?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/WWBmWY/?height=600&default-tab=result&embed-version=2)

我在这里做了一些改动:

```
+ const barColors = ['#000', '#d35f5f', '#fff'];
  barGroups
    .selectAll('rect')
    .data(data)
    .enter()
    .append('rect')
-   .attr("fill", "#d35f5f") +   .attr('fill', (d, i) => barColors[i])
+   .attr('stroke', '#000')
    .attr('class', 'bar')
    .attr('x', d => xScale(d.name))
    .attr('y', d => yScale(d.value))
    .attr('width', xScale.bandwidth())
    .attr('height', d => height - yScale(d.value)); 
```

我添加了`barColors`变量作为一个充满十六进制颜色的数组。我使用了一个匿名函数来选择填充颜色。我还添加了一种笔画颜色，因为我们需要它来显示白条！

我还使 SVG 更宽，并增加了一些图例宽度。否则，图例将被切断！

```
const margin = { top: 20, right: 20, bottom: 70, left: 90 };
const width = 600 - margin.left - margin.right;
const height = 300 - margin.top - margin.bottom; + const legendWidth = 300; 
const svg = d3
  .select("#chart")
- .attr("width", width + margin.left + margin.right) + .attr("width", width + margin.left + margin.right + legendWidth)
  .attr("height", height + margin.top + margin.bottom); 
```

但是我们还没有完成！我们还需要添加图例！我在这里通过错误学到的东西是，我们必须对代码进行一点重构。我是一个有点 d3 的新手。很多时候是我漫无目的地尝试一些事情，然后意识到我需要采取一些不同的方法。这里我需要做的是这样重构它:

```
+ const g = barGroups
+   .selectAll('g')
+   .data(data)
+   .enter()
+   .append('g') 
- barGroups
-   .selectAll("rect")
-   .data(data)
-   .enter()
-   .append("rect") + g.append('rect')
  .attr('fill', (d, i) => barColors[i])
  .attr('stroke', '#000')
  .attr('class', 'bar')
  .attr('x', d => xScale(d.name))
  .attr('y', d => yScale(d.value))
  .attr('width', xScale.bandwidth())
  .attr('height', d => height - yScale(d.value)) 
```

我们需要将多个`<rect>`元素绑定到同一个数据。我决定将数据绑定到`<g>`上，并添加我需要的任何元素。我想对图例使用相同的数据绑定，所以我使用了它！

所以我开始添加一些新的`<rect>`和`<text>`标签来制作图例！

```
const lineItemHeight = 30
g.append('rect')
  .attr('fill', (d, i) => barColors[i])
  .attr('stroke', '#000')
  .attr('width', 20)
  .attr('height', 20)
  .attr('x', width + margin.right)
  .attr('y', (d, i) => lineItemHeight * (i + 1))

g.append('text')
  .text(d => `${d.name} - ${d.value}`)
  .attr('x', width + margin.right + 30)
  .attr('y', (d, i) => lineItemHeight * (i + 1) + 15) 
```

现在我们有了一些反映实际数据和标签的文本。我们要做的最后一件事是格式化数字，以便它读起来很好。

```
g.append('text') - .text(d => `${d.name} - ${d.value}`) + .text(d => `${d.name} - ${d3.format(".2s")(d.value).replace("G", "B")}`) 
```

现在给图例加个标题，说 B =十亿。

```
const svg = d3
  .select("#chart")
  .attr("width", width + margin.left + margin.right + legendWidth)
  .attr("height", height + margin.top + margin.bottom)
  .attr('aria-labelledby', 'title');

+ svg.append('text')
+  .text('Legend')
+  .attr('x', width + margin.right + margin.left)
+  .attr('y', 20) 
+ svg.append('text')
+  .text('B = billion')
+  .attr('x',width + margin.right + margin.left)
+  .attr('y', 40) 
```

我们希望调整图例项的位置，因为图例标题和图例项占用了一些空间。

```
g.append('rect')
  .attr("fill", (d, i) => barColors[i])
  .attr("stroke", "#000")
  .attr('width', 20)
  .attr('height', 20)
  .attr('x', width + margin.right)
- .attr('y', (d, i) => lineItemHeight * (i + 1)) + .attr('y', (d, i) => lineItemHeight * (i + 1) + 30) 
g.append('text')
  .text(d => `${d.name} - ${d3.format(".2s")(d.value).replace("G", "B")}`)
  .attr('x', width + margin.right + 30)
- .attr('y', (d, i) => lineItemHeight * (i + 1) + 15) + .attr('y', (d, i) => lineItemHeight * (i + 1) + 45) 
```

下面是最终结果！

[https://codepen.io/littlekope0903/embed/dLBYaN/?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/dLBYaN/?height=600&default-tab=result&embed-version=2)

## 添加更多上下文

我用希瑟·米格里奥利西的图形代码笔作为这篇文章的灵感。现在，屏幕阅读器有了我们可视化的文本版本。然而，我注意到她对咏叹调的奇妙运用，为她的图表增加了更多的内容。我将采用一些与她相同的原则，并用 d3 将它们应用到这个图中(她用简单的 SVG 写了她的)。

我要做的第一件事是给我的 SVG 添加一个标题。

```
const svg = d3
  .select("#chart")
  .attr("width", width + margin.left + margin.right + legendWidth)
  .attr("height", height + margin.top + margin.bottom)
+ .attr('aria-labelledby', 'bar-chart-title'); 
+ svg.append('text')
+  .text('2018 Fruit Production')
+  .attr('id', 'bar-chart-title')
+  .attr("x", margin.left)
+  .attr("y", 250) 
```

我推荐阅读她关于[可访问 SVG](https://css-tricks.com/accessible-svgs/)的文章，了解为什么这是一个好的实践。她进行了大量的研究，对 SVG 的了解比我还多！

我喜欢她把条形图像列表一样读出来的方式。我也要把这些添加到他们每个人身上！我还将向角色为`list`的组添加一个`aria-label`。

```
const barGroups = svg
  .append("g")
+ .attr('role', 'list')
+ .attr('aria-label', 'bar chart')
  .attr("class", "data")
  .attr("transform", `translate(${margin.left}, 0)`);

const barColors = ["#000", "#d35f5f", "#fff"];

const g = barGroups
  .selectAll('g')
  .data(data)
  .enter()
  .append('g')
+ .attr('role', 'listitem'); 
```

Heather 做了一件我在这里不打算做的事情，就是把`role="presentation"`加到坐标轴上。原因是我在 Twitter 上发布了这个问题，得到了不同的回答。

> ![Lindsey Kopacz 🐞 profile image](img/9b5077daa8beb0b87bde634efb9d325d.png)林赛·科帕🐞@ little kope![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我有一个 [#a11y](https://twitter.com/hashtag/a11y) 感觉很主观的问题。如果您正在创建一个 SVG 图，如果您以不同的方式宣布数据点，轴对屏幕阅读器来说不是有点没用吗？
> 
> 例如，“捐款- $100k”。如果它被读取，这个轴不是多余的吗？2019 年 05 月 01 日下午 12:53[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1123571021120827394)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1123571021120827394)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1123571021120827394)18

我曾经考虑过屏幕阅读器的冗余，但是有人提出了一个很好的观点。

> ![Chris Kitchens profile image](img/c638f4ace894affab7dd6897f0feef9f.png)克里斯后堂[@克里斯后堂](https://dev.to/imchriskitchens)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ little kope](https://twitter.com/LittleKope)不一定。人们并不总是以你期望的方式使用辅助工具。例如，我患有多动症，经常使用屏幕阅读器来帮助我写作。没有视力问题，我只是需要一些东西读给我听，所以我知道这一切都是有意义的。😊2019 年 05 月 01 日 21:58 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1123708336212516864)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1123708336212516864)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1123708336212516864)1

这是我从未想过的事情，即使我自己也患有多动症。也就是说，我决定将轴放在 DOM 中，并在 SVG 中为这些组添加一个`aria-label`。

```
svg
  .append("g")
  .attr("class", "x-axis")
+ .attr('aria-label', 'x axis')
  .attr("transform", `translate(${margin.left}, ${height})`)
  .call(xAxis);

svg
  .append("g")
  .attr("class", "y-axis")
+ .attr('aria-label', 'y axis')
  .attr("transform", `translate(${margin.left}, 0)`)
  .call(yAxis); 
```

[https://codepen.io/littlekope0903/embed/qwepvj/?height=600&default-tab=result&embed-version=2](https://codepen.io/littlekope0903/embed/qwepvj/?height=600&default-tab=result&embed-version=2)

## 结论

我确信我可以大大提高这种形象化！我对 SVG 还比较陌生，其中有些是主观的。尚不清楚轴点是否多余。关于我是否应该对屏幕阅读器隐藏它，我得到了不同的答案。我决定去与更多的上下文更好，除非它很烦人。

你怎么想呢?在 [Twitter](https://twitter.com/LittleKope/) 上告诉我！还有，我现在有一个 [patreon](https://www.patreon.com/a11ywithlindsey) ！如果你喜欢我的作品，考虑成为赞助人。如果你认捐了 5 美元或更多，你将能够对未来的博客文章进行投票！干杯！祝你一周愉快！