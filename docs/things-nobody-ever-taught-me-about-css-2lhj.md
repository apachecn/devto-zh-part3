# 没人教过我关于 CSS 的事情

> 原文：<https://dev.to/devdevcharlie/things-nobody-ever-taught-me-about-css-2lhj>

[![Photo by Jantine Doornbos on Unsplash](img/ec202f2c6100955cc6bebc60f3c6436e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JbmRtkUJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oa23sbxhurgikxbgjlkg.png)

*最初发表在媒体上。*

这篇文章绝不是批评我曾经共事过的任何人，它只是我最近在做一些个人研究时了解到的关于 CSS 的重要事情的一个快速列表。

* * *

很多开发人员似乎不太关心 CSS，这并不是什么新鲜事。你可以通过网上的对话或与朋友和同事交谈来观察它。

然而，在社区中，我们从与同行的知识共享中学到了很多东西，因此，我有时会意识到，关于 CSS，有一些重要的东西我以前从未被告知，因为其他人从未花时间尝试学习更多。

为了解决这个问题，我决定做一些个人研究，并整理出一个我认为有趣且有助于更好地理解和编写 CSS 的概念清单。

这个列表绝对不是详尽无遗的，它只包含了我在过去几天里学到的新东西，我想和大家分享一下，希望它能帮助到其他人。

* * *

## 术语

和每一种编程语言一样，都有特定的术语用来描述概念。CSS 作为一种编程语言，也没有什么不同，学习一些术语对交流很重要，甚至对你的个人知识也很重要。

### 后代组合子

你知道你的风格中选择器之间的小空白吗？它其实是有名字的，名字是**后代组合子**。

[![Descendant combinator](img/b76b5572385c7758b09f3c5250fb7650.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--En_PqqMW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ui378wtfcn0x9ggeo1uu.png)

### 布局、绘制和合成

这些术语与浏览器如何渲染有更多的关系，但它仍然很重要，因为一些 CSS 属性会影响渲染管道的不同步骤。

**1。布局**

布局步骤是计算一个元素在屏幕上占据多少空间。修改 CSS 中的“布局”属性(例如:宽度、高度)意味着浏览器必须检查所有其他元素并“回流”页面，这意味着重新绘制受影响的区域并将它们重新组合在一起。

**2。油漆**

这个过程是为元素的每个可视部分(颜色、边框等)填充像素的过程。绘制元素通常在多个层上完成。

更改“paint”属性不会影响页面的布局，因此浏览器会跳过布局步骤，但仍会进行绘制。

油漆通常是管道中最昂贵的部分。

**3。复合材料**

合成是浏览器需要以正确的顺序绘制图层的步骤。由于一些元素可能会相互重叠，因此这一步对于确保元素按照预期的顺序出现非常重要。

如果您更改一个既不需要布局也不需要绘制的 CSS 属性，那么浏览器只需要进行合成。

关于什么不同的 CSS 属性触发的细节，可以看一下 [CSS 触发器](https://csstriggers.com/)。

* * *

## CSS 性能

### 后代选择器可能很贵

根据您的应用程序有多大，只使用后代选择器而不使用更多的特性可能会非常昂贵。浏览器将检查每个后代元素是否匹配，因为关系不限于父元素和子元素。

例如:

[![Example of descendant selector](img/746f3329b144dc24b2bb96ecf4011c04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7dvkv1CV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubzug89l5pc3i5hu1toy.png)

浏览器将评估页面上的所有链接，然后才确定真正在我们的`#nav`部分中的链接。

更有效的方法是在我们的`#nav`元素中的每个`<a>`上添加一个特定的选择器`.navigation-link`。

### 浏览器从右向左读取选择器

我觉得我应该知道这个，因为它听起来很重要，但我不知道…

解析 CSS 时，浏览器从右向左解析 CSS 选择器。

如果我们看下面的例子:

[![The browser reads from right-to-left](img/62bbba95b0a42097713c451138345b0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CEagFMkW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6wgv7edvc01pw2pet41w.png)

采取的步骤是:

*   匹配页面上的每个`<a>`。
*   找到包含在一个`<li>`中的每一个`<a>`。
*   使用先前的匹配，并缩小到包含在`<ul>`中的匹配。最后，将上面的选择过滤到包含在具有类`.container`的元素中的选择。

查看这些步骤，我们可以看到，正确的选择器越具体，浏览器过滤和解析 CSS 属性的效率就越高。

为了提高上面例子的性能，我们可以在`<a>`标签上添加类似于`.container-link-style`的东西来代替`.container ul li a`。

### 尽可能避免修改布局

对一些 CSS 属性的更改将需要更新整个布局。

例如，`width`、`height`、`top`、`left`(也称为“几何属性”)等属性需要计算布局并更新渲染树。

如果你改变了很多元素的属性，那么计算和更新它们的位置/尺寸将会花费很长时间。

### 小心颜料的复杂性

一些 CSS 属性(例如:模糊)在绘画时比其他属性更昂贵。思考其他更有效的方法来达到同样的效果。

### 昂贵的 CSS 属性

有些 CSS 属性比其他属性更昂贵。这意味着他们需要更长的时间来绘画。

这些昂贵的房产包括:

*   `border-radius`
*   `box-shadow`
*   `transform`
*   `filter`
*   `:nth-child`
*   `position: fixed`

这并不意味着你不应该使用它们，但这是一个理解的问题，如果一个元素使用这些属性中的一些，并且将渲染数百次，它将影响渲染性能。

* * *

## 订购

### CSS 文件中的顺序关系重大

如果我们看下面的 CSS:

[![](img/ec4994b5d0ee1af29b6abce49f14c8a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JP5F1eEN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0bb5vb6xwjbsosev1hd7.png)

然后看看这段 HTML 代码:

[![](img/39ed335d04bb907bb1d1f768e2306357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jxH99faV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/smk15yn0b1mmtx509o0y.png)

HTML 中选择器的顺序并不重要，重要的是 CSS 文件中选择器的顺序。

* * *

评估 CSS 性能的一个好方法是使用浏览器的开发工具。

如果你使用的是 Chrome 或者 Firefox，你可以打开开发者工具，进入 Performance 标签页，记录下你加载页面或者与页面交互时发生的事情。

[![Snapshot of what the Performance tab can give you in Chrome](img/4164c381f2d5fd6220e1034d1d86c762.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f4OPX8y_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2fub1aio8a151m4tjpis.png)

* * *

## 资源

在为这篇文章做一些研究的时候，我偶然发现了下面列出的一些非常有趣的工具:

[CSS 触发器](https://csstriggers.com/) —一个网站，列出了一些 CSS 属性以及在应用程序中使用和修改这些属性对性能的影响。

从 css 中移除不使用的样式的工具。

[Css-explain](https://github.com/josh/css-explain) —一个解释 Css 选择器的小工具。

[Fastdom](https://github.com/wilsonpage/fastdom) —一个批处理 dom 读/写操作以加速布局性能的工具。

* * *

暂时就这样吧！希望有意义！

感谢阅读！🙏