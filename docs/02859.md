# 用 JavaScript 动态填充 CSS 网格

> 原文：<https://dev.to/kaelscion/dynamically-filling-in-a-css-grid-with-javascript-5geb>

为什么你好！今天，我终于提交了。正如你们许多人所知，我是一名 Python 开发人员。然而，我已经下定决心，从现在开始，我不会外包我的前端开发，并强迫自己学习 JavaScript。

***然而*** ，我拒绝从学习一个“新的热门”框架或工具系统开始(反应过来的伙计们，停止打字。我只是还没有深入研究，抱歉。一次一个奇迹😛)并将只使用 ES6 标准的 Javascript。请注意，我今天才真正开始写任何真正有用的 JS 代码，所以会有很多“嘿，你可以做得更好！”代码示例中的机会。但是，我真诚地请求您继续关注*如何能够做得更好，以便我能够学习正确的方法，我提前感谢这个美好的社区，感谢他们可能提出的任何*建设性的*批评！记住这一点，让我们开始吧！*

那么，我决定先尝试什么小项目呢？我想到了很多选择。但最终，我决定尝试找到白痴矩阵和网格遍历算法的实际用途，我们所有人都被迫在开发人员面试过程的白板挑战回合中这样做。我将要分享的这个小项目简单地创建了一个 CSS 网格，其中包含指定数量的相等的行和列。然后，一个单独的脚本将伪随机(但有限)大小的方块放入网格中的伪随机 x 和 y 位置。这创建了一个有趣的随机页面部分，它在每次页面加载时都会改变。我玩得很开心，希望你也一样！我妻子和我的开发工作室的个人网站将很快在我们的作品集页面上推出这一版本，以我们过去项目的缩略图形式显示这些区块。

这个项目也可以在 GitHub 上获得，任何人都可以免费为自己的项目进行修改。如果你最终使用它，请告诉我们你创造了什么，因为我们看到你们创造的比我们自己创造的更开心！

首先，让我们来布置一下我们的 HTML 页面，它只不过是我们 javascript
的一个容器

```
<html>
    <head>
        <script type="text/javascript" src="gridTraversal.js"></script>
    </head>
    <body>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

很简单。甚至是最基本的。做完这些，我们就可以开始有趣的事情了。由于我是代码划分的大力提倡者，我们将编写三个函数，一个用于创建保存网格的 div，一个用于布置网格的行和列，最后一个用于放置块。我们的网格创建 div 是用下面的 JS 代码
创建的

```
const createGrid = () => {
    const markup = `
    <div class="container" id="container" style="display: grid;
                                                 border: 1px black solid; 
                                                 height:100%; 
                                                 width: 100%">
    </div> `
    document.body.innerHTML += markup
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这是非常 ES6 的箭头功能和模板字符串。但是在整个项目中，除了 JS 核心之外，我们没有导入任何库或框架。

我发现，对我个人来说，在不涉及 JSX 的情况下用 JS 创建 HTML 的最简单的方法是简单地将标记代码写成一个变量(在这里是`const markup`)并通过`document.body.innerHTML += markup`将其附加到主体的 DOM 中。

这个脚本所做的就是创建一个占据整个视口的 div，它有一个黑色边框的网格显示。

为了布置网格的列和行，我们需要这个人

```
const layoutGrid = (height, width) => {
    const container = document.getElementById('container')
    container.style.gridTemplateColumns = `repeat(${width}, 1fr)`
    container.style.gridTemplateRows = `repeat(${height}, 1fr)`
} 
```

Enter fullscreen mode Exit fullscreen mode

熟悉 JS 的人应该能很清楚地看到它的作用。对于那些不熟悉 JS 的人来说，这个脚本从 DOM 中选择了我们的网格容器，并为它分配了两种样式，`gridTemplateColumns`和`gridTemplateRows`，并使用 CSS `repeat`函数通过`fr` css 单元分配大小相等的列和行。列和行的数量由传递给`layoutGrid`函数的`height`和`width`参数决定。

现在我们有了一个包含网格的网格 div。街区呢？以下脚本解决了这个问题:

```
const fillGrid = (x, y, blockSize, numOfBlocks, color) => {
    const container = document.getElementById('container')
    const test = Array(num_of_blocks).keys()
            for (var i of test){
                let markup = `<div id="card ${i}" 
                                   style="grid-column: ${Math.floor(Math.random() * y) + 1} / span ${blockSize}; 
                                          grid-row: ${Math.floor(Math.random() * x) + 1} / span ${blockSize};
                                          background-color: ${color};
                                          border: 1px black solid;">${i}</div>`
                container.innerHTML += markup
            };
} 
```

Enter fullscreen mode Exit fullscreen mode

致 pro JS devs:请温柔一点。致新 JS devs:对不起。我不确定这是什么，但我觉得这个剧本很丑。先不说这个，至少让我解释一下它的作用。

首先，我们定义一个名为`fillGrid`的函数，它接受参数`x`、`y`、`blockSize`、`numOfBlocks`和`color`。`x`和`y`定义了我们将要放置的每个方块的起始轴。`blockSize`确定每个块将在网格中占据多少空间。`numOfBlocks`是我们将要扔进这个网格的块的数量，最后，`color`是告诉浏览器这些块的背景颜色。

我们的第一行选择包含网格的 DOM 元素。然后，我们生成一个包含`numOfBlocks`项的数组，并使用 for...创建我们的块的循环。

每个块的大小和位置由 Math.floor()函数定义(该函数生成伪随机整数),介于 0 和由`blockSize`参数确定的最大块大小之间。

然后，每个块接收一个 1px 的黑色实心边框和指定的背景色，并被追加到网格的标记中。

现在，让我们将脚本包含在 HTML 文档中，就像这样

```
<html>
    <head>
        <script type="text/javascript" src="gridTraversal.js"></script>
    </head>
    <body>
        <script>createGrid();</script>
        <script>layoutGrid(5, 5)</script>
        <script>fillGrid(5, 5, 4, 2, "green")</script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

运行脚本，看热闹！每次页面加载(或刷新)都会重新排列网格中的块。摆弄积木的大小、放置的积木数量以及网格中的列数和行数！很多时候，这些区块重叠在一起，形成了看起来非常独特的网格布局。试试看，让我知道你得到了什么！

如果你喜欢这类内容，记得给这个帖子一个赞，评论，或者把它收藏起来以便阅读，这样我就知道你们接下来想看什么了！非常感谢您的阅读，我期待着在未来为您提供更多的内容！