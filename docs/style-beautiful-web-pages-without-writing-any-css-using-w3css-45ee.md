# 不用写任何 CSS 就能设计出漂亮的网页。使用 W3.CSS。

> 原文：<https://dev.to/imalittletester/style-beautiful-web-pages-without-writing-any-css-using-w3css-45ee>

最近，我在寻找一种方法来创建一些网页，在我即将到来的技术研讨会上用作演示页面。我不想花太多时间来设计它们，但同时我想让它们看起来漂亮而现代。在这一点上使用 CSS 是不可能的，因为对于我想要创建的东西来说，这太费力了。偶然发现了一个完全符合我意愿的东西，就是 W3.CSS.
W3。CSS 是一个现代的，易于学习的 CSS 框架，它允许跨各种浏览器和平台的响应和工作。

# 在你的项目中使用它

为了使用这个框架来设计页面样式，您只需要通过在 HTML 页面中添加以下条目来导入它:

```
<link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css"> 
```

# 好吧，但是你能有什么风格呢？

为了全面了解这个框架提供了什么，你可以查看他们的[介绍页面](https://www.w3schools.com/w3css/w3css_intro.asp)。你可以设计按钮、导航条、下拉列表和列表，等等。您还可以应用预定义的颜色，或在图像上应用色调效果。当然，您可以轻松地应用填充或边距，甚至可以在页面上设置布局，以帮助项目放置在正确的位置。或者左边，如果你想的话。

# 嗯嗯，有意思，但是如何？

它的总结是:对于任何你想要样式化的 HTML 元素，你需要添加一个 class 属性。类属性的值将决定样式。例如，要向元素添加预定义的颜色，如青色，元素的类需要是“w3-cyan”。要添加简单的边框，您需要添加“w3-border”类。检查[“W3。CSS" site](https://www.w3schools.com/w3css/default.asp) 获取所有这些值和所有可以应用的样式。

# 这篇文章的封面图片呢？

啊是的。此图像仅使用“W3。CSS ”,只是为了让您体验一下这个框架。

我来告诉你我是用什么创造的吧。按钮(在左边)和图像(在右边，连同它下面的文本)被放置在一个 *div* 元素中，看起来像这样:

```
<div class="w3-display-middle w3-container w3-border-top w3-border-bottom w3-margin w3-border-teal"> 
```

这意味着:

*   *w3-显示-中间* -元素将被放置在页面的中间。可以显示元素:左上、中上、右上、左、中、右或左下、中下和右下。
*   *w3-container* -一个相对于任何其他元素左右填充 16px 的元素。
*   *w3-border-top* 和 *w3-border-bottom* -添加上下边框。边框的颜色，蓝绿色，由 *w3-border-teal* 给出。你也可以创建圆形边框，或者粗边框。
*   *w3-边距* -给这个容器的所有边增加 16px 的边距。

容器内左侧的按钮有以下 HTML 代码:

```
<button class="w3-btn w3-border w3-teal w3-margin w3-padding-large w3-left w3-large w3-opacity">Who's
        stylish?</button> 
```

让我们看看这一切意味着什么:

*   创建一个矩形按钮，当鼠标悬停时，会显示一个漂亮的阴影。
*   *w3-border* -为按钮提供细边框。
*   *w3-蓝绿色* -将按钮颜色设置为蓝绿色。有关其他可用的颜色，请参见“颜色”。
*   *w3-left* -相当于 CSS 中的 *float:left* 。
*   *w3-large* -指定按钮的文本大于默认的文本大小。
*   *w3-不透明* -给按钮一个透明效果。请参阅“效果”了解更多详情和其他值。

现在，对于图像的右侧:

```
<div class="w3-card-4 w3-border w3-left w3-padding-large w3-margin w3-teal">
        <img src="https://farm2.staticflickr.com/1870/44322750771_d9eb2522f8_n.jpg"
             class="w3-sepia-min w3-round-xlarge">
        <div class="w3-container w3-center">
            <h4><b>We are. Yes we are.</b></h4>
        </div>
</div> 
```

如您所见，图像(一个 *img* 标签)和它下面的文本被放在一个样式化的 *div* 中。定义这个 *div* 做什么的属性是 *w3-card-4* 。它指定 *div* 是一种容器(主要用于创建类似“纸卡”的容器)，阴影边框为 4px。card *div* 元素也指定了边框、浮动位置、填充、边距和背景色(我已经在上面的元素中描述过了)。

该图像使用了两个属性:一个是棕褐色的颜色效果，这是由 *w3-sepia-min* 属性给出的，它的角根据 *w3-round-xlarge* 属性变得很圆。

对于显示在图像正下方的文本，创建另一个 *div* 元素来包含它，类型为 *w3-container* 。属性 *w3-center* 指定来自 *div* 内部的所有文本将显示在相对于 *div* 的居中位置。文本本身只是一个粗体字。

已经困惑了？请不要犹豫，来看看 [W3。CSS 站点](https://www.w3schools.com/w3css/default.asp)提供所有的细节和选项，让你轻松创建时尚的网页。