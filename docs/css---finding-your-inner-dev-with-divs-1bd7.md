# HTML & CSS -用 Divs 找到你的内心世界

> 原文：<https://dev.to/crishanks/css---finding-your-inner-dev-with-divs-1bd7>

# 引导你内心的发展

"如果你喜欢 CSS，你可能是一个前端开发人员."自从深入研究像 Ruby on Rails 这样的全栈框架以来，这个短语就一直在校园里流传。

随着我对新技术的尝试，我发现了更多我想成为的开发人员的类型。毫无疑问，这是作为一名科技学生最令人兴奋也最令人畏惧的方面之一。作为一个非常关心用户体验的好奇开发者，我决定自己“div”成一个小小的 CSS。

## 项目

事实证明，你可以用一些普通的 HTML 和 CSS 创建一个漂亮的静态网站。以下示例是用一个 index.html 和 style.css 文件制作的。我想对一个你们可能还记得的旧址大声疾呼可能会很有趣:

[https://www.youtube.com/embed/SxMzCLEEe1E](https://www.youtube.com/embed/SxMzCLEEe1E)

## 设置

1.  建立一个[index.html 文件](https://www.w3schools.com/hTml/html_basic.asp)，就像你对任何其他项目所做的那样。

2.  在同一目录下创建一个 style.css 文件。为了链接 CSS 样式表，您需要通过 index.html 文件头部分中的 link 标记来链接它(下面的第 7 行)。

[![HTML Header](img/76c558e361d42423d134383993e8db5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M10rCZ0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yn1nnvtjghfsq51ca9mc.png)

和所有伟大的朋友一样，你的 HTML 将会监听并响应 CSS 文件中的样式。

## 用 div 组织 HTML

在 index.html，你基本上是在告诉你的浏览器你想要显示什么信息。有时候网页会显示很多信息，如果我们不把它组织好，你的 CSS 几乎不可能准确地识别每个 HTML 元素应该在哪里，它应该看起来像什么，甚至它应该做什么。

输入 div 标签。div 长这样:`<div></div>`。它们本质上是 HTML 代码段的容器，您希望这些代码段生活在一起并具有相似的行为。我们经常嵌套 div，所以子 div(其他 div 中的 div)可以继承外部父 div 的品质，但也可以被赋予独特的品质。

[![HTML Header](img/8275f5978529cedbe7a90db2da718da4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BFI5jMMv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sxs8ktm0mp2jx7rzpkpw.png)

看看第 13-15 行。我有很多独特的侧边栏内容，但我希望侧边栏中的所有元素都包含在同一个区域。所以，我把这些碎片套在一起。

## 选择器&特异性

在上面的 div 标签中，您会注意到我添加了`class='sidebar'`和`id='sidebar_content'`。style.css 文件会有相应的语法，`.sidebar`和`#sidebar-content`。这些被称为选择器。它们是你的 CSS 告诉你的 HTML 要样式化哪些信息的方式。

[![HTML Header](img/af929a7a95533d0e5027454000c1156b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hOfXLk4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6mygqyr5dwpbku02lxit.png)

选择器让我们变得吹毛求疵。如果我想选择侧边栏中的所有图像，我可以使用`.sidebar img`。相当酷。但是，如果我只想选择一个特定的元素(图片、段落、列表项等)，该怎么办呢？)在侧边栏部分？选择器遵循特定规则，这意味着某些类型的标签比其他标签具有更高的权重。链接在一起的选择器组合分数，并且最高特异性分数获胜。

特异性是这样计算的:

*   元素(`<h1>, <p>, <img>`等)。):1 分
*   类别(用`.`表示):10 分
*   IDs(用`#`表示):100 分

注意，万能`*`选择一切，不考虑分数。我们尽量少用。

让我们来看一个例子:

[![HTML Header](img/f8f2508c253de33d5f4563d9f8d27927.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LkBY65Wm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8fiwawcs13rjgq8f07ux.png)

你能计算出特异性吗？因为`.hero-text`类选择器值 10 分，而`h3`元素选择器值 1 分，所以`.hero-text h3`选择器(11 分)获得了与`.hero-text` div 中任何其他元素不同的特殊样式。对于额外的练习， [W3 学校](https://www.w3schools.com/css/css_specificity.asp)有一些很棒的信息。

## 而我连 JS 都不用？

CSS 选择器让我们变得相当棘手。我们可以使用名为[伪类](https://www.w3schools.com/css/css_pseudo_classes.asp)的选择器来告诉我们的 HTML 什么时候让一个元素看起来像某种方式。我们可以指定像`:visited`、`:hover`、`:active`、`:lang`等动作。以我们在按钮和链接上看到的悬停和点击为例。通过使用下面的选择器，我们的 CSS 能够告诉我们的 HTML 在鼠标悬停和点击时改变颜色甚至背景。

使用伪类，当鼠标悬停在博客内容上时，我甚至能够展开博客内容中的图像。

[![HTML Header](img/f014316ee9e55323b6fb378270fb8986.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EzERK_th--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t2mak6aztbu0irfenf06.png)

## 结论

对于任何想知道前端 Web 开发是否适合他们的人，我建议花一天时间投入到个人 CSS 项目中。

网页设计具有创造性、艺术性和趣味性。这也可能非常棘手和令人沮丧。在我的第一个项目中与 CSS 斗争之后，我对漂亮的 web 应用程序有了极大的欣赏。我觉得有责任让互联网看起来干净有趣，运行顺畅。我想你也会的。