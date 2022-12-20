# 重写一个老项目！第 1 部分:HTML 和 CSS

> 原文：<https://dev.to/kenbellows/rewriting-an-old-project-part-1-html-css-4o5p>

# 背景故事

我是一个已经在网络上呆了一段时间的人。我在 12 岁的时候学会了 JavaScript(这里说的是 2003 年，IIRC ),当时我在我父亲的书架上发现了 Steven W. Disbrow 的 JavaScript 周末速成班 T2 T3，我坐在我父亲从他公司 it 工作的垃圾堆里为我抢救出来的 Windows 95 机器前，打开记事本和 IE6，开始不停地编程。据我回忆，这大概是让我着迷的代码:

```
for (var i=0; i<10000000000000; i++) {
    document.write(i + '  ');
} 
```

当然，它让我着迷的原因是，当我在条件中的数字上加了足够多的零时，它让我的浏览器崩溃了。这意味着我是一个

<figure>

```
..................................................................................
..................888.....................888.....................................
..................888.....................888.....................................
..................888.....................888.....................................
..................88888b...8888b....d8888b888..888..d88b..888d888.................
..................888."88b...."88bd88P"...888..88Pd8P..Y8b888P"...................
..................888..888.d888888888.....888888K.88888888888.....................
..................888..888888..888Y88b....888."88bY8b.....888.....................
..................888..888"Y888888."Y8888P888..888."Y8888.888.....................
..................................................................................
.............._____________________________________________________...............
....._,aad888P""""""""""Y888888888888888888888888888888P"""""""""""Y888baa,_......
....aP'$$$$$$$$$$<figure""Ybaa,.........,aadP""'$$$$$$$$$$<figureYa.....
...dP$$$$$$$$$$$$$$<figure"b,.....,d"'$$$$$$$$$$$$$$$Yb....
...8l$$$$$$$$$$$$$$$$8l_____8l$$$$$$$$$$$$$$$$8l...
..[8l$$$$$$$$$$$$$$$$8l"""""8l$$$$$$$$$$$$$$$$8l]..
...8l$$$$$$$$$$$$$$$d8.......8b$$$$$$$$$$$$$$$$8l...
...8l$$$$$$$$$$$$$$$dP/.......\Yb$$$$$$$$$$$$$$$8l...
...8l$$$$$$$$$$$$$$,dP/.........\Yb,$$$$$$$$$$$$$$8l...
...8l$$$$$$$$$$$$,adP'/...........\`Yba,$$$$$$$$$$$$$8l...
...Yb$$$$$$$$$$$,adP'...................`Yba,$$$$$$$$$$$dP....
....Yb$$$$$$$$,aadP'.........................`Ybaa,$$$$$$$$dP.....
.....`Yb$$$$$,aadP'.................................`Ybaa,$$$$$dP'/.....
.......`Ybaaaaad8P"'.........................................`"Y8baaaaadP'........
.................................................................................. 
```

<figcaption>hacker 😎
(btw, ascii art lifted from [here](http://ascii.co.uk/art/hacker) and [here](https://www.asciiart.eu/clothing-and-accessories/glasses), with some modifications)</figcaption>

</figure>

总之。快进 8 到 9 年。我在读大学，高三，2012 年吧，逃避工作。我从未失去对网络的热爱，尽管我对它有点保守，因为它是“JS Sucks”运动的高潮。但是我经常花一部分拖延时间在小的 JS 项目上以获得成功。我偶然看到一个教程(可能是[这个是](http://rectangleworld.com/blog/archives/298)，虽然我不确定)讲述相对较新的 HTML5 `<canvas>`以及如何使用不同灰度的点来模拟深度，制作一个小的人造 3D 球体。而且我觉得这个结果很酷，所以我决定不看教程，自己试着弄明白。

我做了，我对结果很满意。我甚至添加了一些原版没有的交互性，这很有趣。在这里，复制粘贴到一个代码笔:

[https://codepen.io/kenbellows/embed/MxZrjy?height=600&default-tab=result&embed-version=2](https://codepen.io/kenbellows/embed/MxZrjy?height=600&default-tab=result&embed-version=2)

现在回头看代码，对于当时来说还是不可怕的。但是伙计，它需要一些更新。2012 年是很久以前的事了，从那以后我和网络都有了显著的进步。

所以我想我应该更新代码，这似乎是一个有趣的想法，在我走的时候写下来，看看自 2012 年以来我们美好的网络世界(www)发生了什么变化。

如果你想跟进，回购在这里:[https://github.com/kenbellows/jsphere/](https://github.com/kenbellows/jsphere/)T2，演示在这里:[https://kenbellows.github.io/jsphere/](https://kenbellows.github.io/jsphere/)

回购现在将新代码推送到`master`和`gh-pages`分支，但我将保留名为 [`gh-pages__old`](https://github.com/kenbellows/jsphere/tree/gh-pages__old) 的分支中的先前状态，因此在那里检查大约 7 年基本上未被更改的完整先前代码。

**重要提示:**我不打算在这里讨论代码的*内容*，代码做了什么，只是我如何更新了结构和语法等等。然而，我确实计划写一篇最后的文章，讨论代码实际上做了什么，并以适当的教程风格浏览它。

# HTML

所以首先。那个 HTML。不太好。下面是结构，非常简略:

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <script type="text/javascript" src="./js/jquery-1.7.1.min.js"></script>
        <script type="text/javascript" src="./js/jquery.event.drag-2.0.min.js"></script>
        <script type="text/javascript" src="./js/sphere.js"></script>
        <link rel="stylesheet"...>
    </head>
    <body>
        <!-- github link banner omitted -->

        <div id="header">
            <h1 id="header-title">jSphere</h1>
            <h2 id="header-subtitle">Just to see what I could do.</h2>
        </div>
        <div id="controls">
            <h3 id="controls-title">Controls</h3>
            <div id="controls-body">
                <p id="c-1">Rotate:  Click and drag</p>
                <p id="c-2">Pan:     Hold shift, click and drag</p>
                <p id="c-3">Zoom:    Hold ctrl, click and drag</p>
            </div>
        </div>
        <div id="content">
            <canvas id="canvas" width="800" height="700"></canvas>
            <p id="psst">Psst. There are a couple (that's 2) hidden key combos that do some things that I found by accident, so play around.</p>
        </div>
    </body>
</html> 
```

好吧。反思:

1.  ~~不需要把脚本标签放在头中。把它们放在身体的末端。这消除了对 jQuery `$(function(){ ... })`包装器的需求。~~ **更新:**正如@crazytim 的一篇评论所指出的，这种做法*也*相当过时！在`<body>`末尾的`<script>` s 直到 HTML 的其余部分被解析和处理后才能开始下载，这会减慢速度，对此有一个标准的解决方案:[的`defer`属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#attr-defer)，它告诉浏览器下载文件，这样它就准备好了，但是要等到 DOM 被完全解析后再运行它。正是我需要的！所以我应该把我的脚本保存在`<head>`中，但是使用`<script defer ...>`，它去掉了`$(function(){ ... })`包装器。谢谢蒂姆。
2.  另外，我们不再需要 jQuery 了。以前，这让世界变得简单多了，但是现在没有必要了，尤其是对于像这样小的项目。我很确定我导入它只是为了使用`$()`元素选择器函数和`jquery.event.drag`插件，这两个现在都可以取消了。
3.  [语义 HTML](https://dev.to/kenbellows/stop-using-so-many-divs-an-intro-to-semantic-html-3i9i) ！我再也不会写另一个`<div id="header">`了。就用`<header>`！`<div id="content">`也一样:基本上就是`<main>`而已。
4.  *男人*我爱`id` s！*身体里的每一个元素*都有一个`id`！那太不必要了。我们有一个丰富的 CSS 选择器语法(当时也是这样)来负责在页面上找到正确的东西。

所以，这就是我如何重组页面。我已经折叠了`<main>`块中的`id="controls"`和`id="content"`，并对其内部进行了重大的重新排列，这也将反映在 CSS 中，但我们将在下一步到达:

```
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <link rel="stylesheet" type="text/css" href="./css/sphere.css">
        <link rel="stylesheet" type="text/css" href="./css/fonts.css">
        <script defer type="text/javascript" src="./js/sphere.js"></script>
    </head>
    <body>
        <!-- github link banner omitted -->

        <header>
            <h1>jSphere</h1>
            <h2>Just to see what I could do.</h2>
        </header>
        <main>
            <section id="controls">
                <h3>Controls</h3>
                <ul id="controls-listing">
                    <li>Rotate:  Click and drag</li>
                    <li>Pan:     Hold shift, click and drag</li>
                    <li>Zoom:    Hold ctrl, click and drag</li>
                </ul>
            </section>
            <canvas id="canvas" width="800" height="700"></canvas>
            <p class="psst">Psst. There are a couple (that's 2) hidden key combos that do some things that I found by accident, so play around.</p>
        </main>
    </body>
</html> 
```

巨大的变化是:

1.  ~~将脚本移动到正文的末尾~~(见上面的注释)将`defer`属性添加到`<script>`标签中，以消除对任何`onready`类型事件处理程序的需要。
2.  已删除 jQuery。我将不得不重做依赖于`jquery.event.drag`插件的鼠标交互代码，不过没关系；不管怎么说，现在容易多了。
3.  重构标记以使用语义元素`<header>`、`<main>`和`<section>`。重新构建了代码，使其更具语义性。之前的结构受当时可用的 CSS 布局模式的影响很大(或者说没有)，所以现在做我想做的事情会简单得多。
4.  就这一点而言，几乎摆脱了所有的`id`。我留下的唯一一个是用于`<section>`标签的，我确实认为部分需要标签；通常不止一个，它们经常需要在 CSS 中被特别选择，所以它们在 IMO 中有价值。

好了，现在让 CSS 来匹配。

# CSS

这是以前的东西。请记住，它与上面的第一个 HTML 示例一起提供。我跳过了字体和颜色的东西，因为它们都很标准，保持不变。有趣的是与上面重构的 HTML 相关的布局和选择器。

```
#header {
    position: relative;
    width: 100%;
}
#header-title, #header-subtitle {
    width: 500px;
}
#header-subtitle {
    font-style: italic;
}

#content {
    padding: 0px;
    width: 810px;
    height: 900px;
    margin: 0px auto;
    position: relative;
    top: -50px;
}

#canvas {
    border: 2px solid black;
    z-index: 2;
}

#controls {
    width: 810;
    height: 200px;
    margin-left: auto;
    margin-right: auto;
    margin-top: 60px;
    margin-bottom: -50px;
    z-index:1;
    position: relative;
}
#controls-title {
    width: 75px;
    margin-bottom: 15px;
}
#controls-body {
    width: 100%;
    position: relative;
    margin: 0px;
}

#c-1 { /* Rotate */
    position: absolute;
    left: 0px;
    top: 0px;
    margin: 0px;
}
#c-2 { /* Pan */
    position: relative;
    width: 200px;
    text-align: center;
    margin: 0px auto;
    right: 10px;
}
#c-3 { /* Zoom */
    position: absolute;
    right: 10px;
    top: 0px;
    margin: 0px;
}

#psst {
    font-style: italic;
} 
```

呼，有很多绝对定位和固定长度。现在，我想强调的是，这是一个小小的周末黑客，我不想花太多时间，我没有所有的布局方法可供我使用，但仍然...似乎没有必要。我想我太匆忙了。

特别有趣的是描述旋转、平移和缩放控件的三个`<p>`标签。我给了他们每个人一个`id`、`#c-1`、`#c-2`和`#c-3`，并把他们完全定位到我想要的位置。我认为这是由于当时很难将一个项目居中，而将另外两个项目侧向对齐。你可以用桌子来做，但是...即使在 2012 年，桌子也是禁忌。除非万不得已，否则我不敢。绝对定位在我心目中绝对更好。

(现在回想起来，我这样做很傻，一个快速的 CSS 表(我觉得那些当时是支持的？)或者甚至一个 HTML 表都是一个更简洁的解决方案。)

如今，我们在布局上多了两种选择:Flexbox 和 Grid。当重写时，我的第一反应是使用一个带有`justify-items: space-between`的快速伸缩容器，因为它的行数更少。但是在实现之后，我记得这是一个经典的极端情况，Flexbox 的行为与您可能预期的略有不同，除非您真正理解它是如何工作的。

不要误解我，它的行为完全是好的、可预测的和有用的，只是不像我想的那样。我最初在这里写了一篇关于这种区别的解释，将它与 CSS Grid 进行了比较，但结果发现它非常长，所以我在这里发布了它自己的帖子: [Flex 项目不是网格列](https://dev.to/kenbellows/flex-items-are-not-grid-columns-4p2i)

这篇文章的结果是，我在上面新 HTML 的父元素`<ul>`上使用了下面的 CSS:

```
ul#controls-listing {
    grid-auto-flow: columns;  /* automatically place new items in new columns */
    grid-auto-columns: 1fr;   /* auto-columns should be 1fr wide */
} 
```

我使用 auto-flow 方法，而不是使用`grid-template-columns: 1fr 1fr 1fr`、
方法，因为我喜欢让我的代码经得起未来考验，只要它不会增加太多工作，而且这个方法可以灵活地处理任何数量的项目，所以如果我想的话，我可以在以后添加更多的控件。

我还在网格项目上使用了几个`text-align`规则，默认情况下将它们居中对齐，然后左对齐第一个项目，右对齐最后一个项目:

```
ul#controls-listing > li {
    text-align: center;
}
ul#controls-listing > li:first-child {
    text-align: left;
}
ul#controls-listing > li:last-child {
    text-align: right;
} 
```

CSS 的其余变化主要是更新了选择器,( 1)考虑到 HTML 的重构,( 2)尽可能使用元素选择器，而不是所有那些`#id`引用。她的全部最终结果:

```
header > h2 {
    font-style: italic;
}

main {
    max-width: 810px;
    margin: 0px auto;

    display: flex;
    flex-direction: column;
    align-items: stretch;
}

#controls > h3 {
    text-align: center;
    margin-bottom: 15px;
}

ul#controls-listing {
    /** removing the default ul padding*/
    padding: 0;

    /* flexbox fallback if grid is not supported */
    display: flex;
    justify-content: space-between;

    display: grid;
    grid-auto-flow: column;
    grid-auto-columns: 1fr;
}
ul#controls-listing > li {
    list-style: none;
    text-align: center;
}
ul#controls-listing > li:first-child {
    text-align: left;
}
ul#controls-listing > li:last-child {
    text-align: right;
}

canvas {
    border: 2px solid black;
}

.psst {
    font-style: italic;
} 
```

这比 CSS 少了一大截，我希望你会同意它更干净，可读性更好。Flexbox 和 Grid 确实在这一领域有所作为，我对网络的未来感到非常高兴。

# 扣人心弦

这就是 HTML 和 CSS 的内容。JavaScript 完全是另一个蜡球，当然需要自己的帖子。所以我把它留在这里。我希望你喜欢这个；拆开旧代码并祝贺自己在过去 7 年中作为一名开发人员有所提高，这确实有点宣泄，但看到在 CSS 布局方法的新时代，即使像这样非常简单的布局也变得容易得多，这也是非常令人兴奋的。

下次见！