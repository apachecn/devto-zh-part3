# 一致性:用 Plotly 模板化图表

> 原文：<https://dev.to/benjcorc/consistency-templating-charts-with-plotly-27aa>

#### 利用 plotly 的模板属性创建一致的图表。

[![](img/93a82fee4cec938f62a822ca06f8864e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tbNU8ZoB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5fJ1LHO1H-5e_AvjTeiODw.jpeg)

伟大的出版物有伟大的图表。《经济学家》和《纽约时报》仅举两个例子。这些对我来说很突出的原因是**一致性**。当你看经济学家图表时，你知道这是一个经济学家图表。

关于一致性最好的事情是你的图表甚至不需要看起来那么好。在上面的例子中，我们没有看到任何特别的东西。事实上,“教育，岁月”有太多的注解，而且非常的繁忙。但一致性胜过了这一点。它会在你的大脑深处产生一股小小的幸福感，这种幸福感来自于发现世界的某种秩序。

<figure>[![](img/e1a58b09b7a41a3b2d73368930b21551.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HKaktvBh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtnAJ9ccsY-UHB4xbO7lgYg.png) 

<figcaption>经济学家如数家珍的例子</figcaption>

</figure>

一个更关注品牌的人可能会说，这也有助于建立一种熟悉感，就像任何好的家居风格或标志集可能会做的一样。但实际上，这一切都是那么高。

#### 瘾君子对经销商

Plotly 是建立在 D3 上的高级库。它利用了 D3 所有奇妙的制图能力，而没有遭受其痛苦的学习曲线。这是一个很棒的图表平台，但有一个问题是很难创建外观一致的图表。

使用 plotly 进行造型非常简单。整个绘图、数据和样式都包含在一个 JSON 对象中。一旦你添加了数据，就只需要设置各种属性，直到图表开始与你脑海中的相似。然后 JSON 被推过 plotly 的一个库 T1，嘿，很快你就得到你的图表了。

问题是我们必须为下一张图表重新做一遍。上帝禁止接下来的 10 年。当然有批量生产的方法。Plotly 有一个 python 库，使得以编程方式制作图表变得非常容易。目前还没有在 python 库中创建模板的方法。所以我们可以尝试提取 JSON 的样式部分，然后将其重新应用到下一个图表中。但是仅仅写出来是假的。

比方说，您确实创建了一个允许您重新应用样式的工作流。当你意识到你的无标题漫画可能对你没有任何帮助时会发生什么。你如何更新你所有的实时图表到你的新主题？写一个脚本来找到你所有的实时图表，下载，转发，转发，转发，转发。

#### Plotly 的模板属性

从 plotly3.4 开始，我们有了一个可以用来解决所有问题的模板属性。目前手头的文件还不多。这里有几篇介绍性文章和[这里](https://plot.ly/javascript/layout-template/)给出了概述。

本质上，您在自身内部重新创建了图表 JSON。模板可以包含数据、布局、注释和图像作为元素。每个元素应用条件的方式与它们的主图表 JSON 对应元素相同。不同之处在于，如果模板中的元素已经存在于主图表中，则主图表的定义优先。

```
# Unthemed
{
 "data": [
 {
 "x": [
 "giraffes", 
 "orangutans", 
 "monkeys"
 ], 
 "y": [
 20, 
 14, 
 23
 ], 
 "type": "bar"
 }
 ],
 "layout":{
 "template":{
 "layout":{

 }
 }
 }
}

#Themed

{
 "data": [
 {
 "x": [
 "giraffes", 
 "orangutans", 
 "monkeys"
 ], 
 "y": [
 20, 
 14, 
 23
 ], 
 "type": "bar"
 }
 ],
 "layout":{
 "template":{
 "layout":{
 "paper\_bgcolor":"pink"
 }
 }
 }
} 
```

<figure>[![](img/d7ff1d20871fc9b17d8f21f9b5df43b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXL1olbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/839/1%2Al-t0iwwPrcJaMcdpoqAJBg.png) 

<figcaption>未命名</figcaption>

</figure>

在上面的例子中，我们只能在模板中设置纸张背景颜色。生成的图表如我们预期的那样运行。

<figure>[![](img/c82347fce64992b66a21750088bb97a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---3TpBDPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/867/1%2At-spejypR2fJFengI1Q3lQ.png) 

<figcaption>主题</figcaption>

</figure>

如果我们直接在图表的布局组件中设置纸张背景颜色，那么模板的纸张背景颜色将不会呈现粉红色。

#### 主题化多个图表类型

如同为图表布局设置模板一样。我们还可以为部分数据设置默认值。在数据部分，我们可以将模板分配给一种图表类型。在下面的例子中，我们为条形图中的条形颜色设置了一个模板。bar 元素的值是定义每个单独跟踪的字典列表。

```
template":{
 "data":{
 "bar":[{"marker":{"color":"pink"}}],
 }
 "layout":{
 ...
 }
} 
```

这里我们已经设置了条形图的第一个轨迹，我们已经将条形图的颜色设置为粉红色。我们可以对每一个轨迹进行扩展，直到我们建立起一个完整的配色方案。

我们甚至可以进一步扩展它，以包括多种图表类型。这意味着我们可以拥有一个适用于所有图表类型的一致模板。甚至考虑到图表类型之间的差异。下面是从同一个模板创建的两个图表。

```
"template":{
 "data":{
 "bar":[{"marker":{"color":"pink"}},{"marker":{"color":"purple"}}],
 "scatter":[{"marker":{"color":"green"}},{"marker":{"color":"red"}}]
 }
 "layout":{
 ...
 }
} 
```

[![](img/691833e3ba2e6184d846529c0642d980.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--peVnK2I8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/807/1%2AlGR6Bq0Uvtk8_3zuIcvuQQ.png)

[![](img/af18201693b921e0982d5a73bf88a614.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fn0gkPpb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/815/1%2A7ex-Tursq3chCE8SE-lvLQ.png)

#### 从设计中分离数据

这一切都很好，但我们还没有真正解决我们的问题。我们已经使图表主题化变得稍微容易了一点，但是我们仍然需要缝合一个巨大的 JSON 以便 plotly 渲染它。

但是我们没有。

既然我们已经有了模板，我们就不需要在图表中使用所有的属性来设计我们的图表了。所以我们把两者分开。我们可以在主图表 JSON 中保留所有图表的基本数据，比如 x 和 y 值，也许还有一些必需的格式。在一个新的 JSON 中，我们把模板对象。因为模板对象不会改变，所以将两者分开是有意义的。

我们需要组合这两个 JSONs 的唯一一点是当它们被交付给最终用户时。由于 plotly 已经构建了一个 javascript 库，允许单独的数据和布局一起动态呈现，因此不加以利用是愚蠢的。我们只是将数据作为数据传递，将模板作为空 layoutobject 的唯一元素传递。

下面是在这个[站点](https://www.benjamincorcoran.com)上动态呈现图表的 javascript 的一部分。调用一个小的 php 脚本从数据库加载图表数据。这与网站上存储的 templateJSON 结合在一起。

```
var chartJSON = $.ajax(
 {
 url:'/resources/getChart.php?chartID='+chartID,
 dataType:'json',
 type:'GET'
 });
if(window.tmpltJSON == null){
 window.tmpltJSON = $.ajax(
 {
 url:'/resources/plotlyTemplate.json',
 dataType:'json',
 type:'GET'
 });
};

$.when.apply($,[chartJSON,window.tmpltJSON])
 .then(function(){
 var chart = JSON.parse(chartJSON.responseJSON);
 var tmplt = window.tmpltJSON.responseJSON;
 plotDiv = document.getElementById("pie");
 Plotly.newPlot(plotDiv,chart,tmplt,{'responsive':true});
 }
); 
```

现在，我所有的图表在呈现给最终用户时都是主题化的。如果我创建 plotlyTemplate.json，它们将立即用于网站上的所有图表。

所有这些意味着，我们再也不需要担心图表的样式了。我们的设置只能产生一致性。至少在你错误地用手机打开它之前。我是说，这么小的屏幕怎么会造成这么大的伤害。

*最初发表于* [*本杰明·科克兰*](https://benjamincorcoran.com/consistency-templating-charts-with-plotly/) *。*