# JS 的层次...风格混搭

> 原文：<https://dev.to/laurieontech/the-layers-of-js-styles-remix-3178>

我最近有点主题。首先，我们浏览了 Javascript 的各层。然后我们探索了 CSS 的层次[。](https://dev.to/laurieontech/the-layers-of-css-3ae5)

今天，我要把他们两个带到一起！所以，事不宜迟，我给你 Javascript 的层次...风格混搭。

## 我的珍贵款式

在任何 Javascript 项目中，我们都有充满 CSS(或者更少，Sass 等)的样式表。我们可以有适用于整个项目的全局样式表，也可以有导入特定 Javascript 或 HTML 页面的样式表。

所有这些样式表都填充了 CSS 代码，类似于下面的代码片段。

```
a {
    color: black;
} 
```

Enter fullscreen mode Exit fullscreen mode

很容易认为这些是唯一能影响我们网站的风格，但这是 Javascript 的地盘！你要知道，事情很少这么简单。

## NMS 代表的不是我的风格！

除了我们可以通过基本 CSS 使用的样式之外，还有第三方框架在使用，比如 Bootstrap。这些框架旨在提供一些默认的样式，使网站看起来更好，而不需要开发人员做太多的定制 CSS 工作。

```
<button type="button" class="btn btn-primary">Primary</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果你在上面的代码中包含了 bootstrap，这个按钮将会是蓝色的，带有白色的字母，并且有一个更加美观的形状。如果不是呢？谁知道呢！取决于您已经包含在项目中的样式。

[![regular versus bootstrap styled button](img/74db563dcbbeffe25954d76c7fe6087a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w0zCVBP2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/bd2991ab-a5c7-410f-acb5-bf4c690d0318/buttons.png) 
*我只是开玩笑，它很可能看起来像左边的那个*

Bootstrap 有不同的版本，所以使用哪个版本很重要。语法和预期的样式都有变化。除此之外，bootstrap 还有不同的包装器！

例如，React 有自己的引导“包装器”，被亲切地称为 [reactstrap](https://reactstrap.github.io/) 。

## 字体牛逼

另一个有助于“风格”的东西是包含了 glyphicons、font awesome 或其他公共可用的库，使您的站点看起来更加完美。如果你看到“glyphicon”或“fa”这样的关键词，那就是你的问题所在。

请记住，这些图标库包含在 bootstrap 中，但它们很酷，有点独特，所以我想把它们算作一个层。此外，如果您不使用 bootstrap，您可以独立使用它们。

[![Dev font awesome icon](img/825057f1ef23746e40a66af15a4c0db0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oUeZmQJa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--xV3OQSDJ--/c_imagga_scale%2Cf_auto%2Cfl_progressive%2Ch_420%2Cq_auto%2Cw_1000/https://thepracticaldev.s3.amazonaws.com/i/9c4scf438fc7lme8yd0g.png) 
*快看！开发甚至有自己的字体真棒图标了！*

> ![unknown tweet media content](img/789ed805d57765ecd1b411df0f4a1acc.png)![Laurie profile image](img/ed82805e4048b1b025fcda51dccd1038.png)劳里[@ laurieontech](https://dev.to/laurieontech)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ bendhalpen](https://twitter.com/bendhalpern)[@ Healey codes](https://twitter.com/healeycodes)Yay dev 徽章！2019 年 04 月 06 日 23 点 50 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1114676691283120129)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1114676691283120129)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1114676691283120129)

*Here it is in the wild.*

## 内联文体，可 JSX 之乱

好，下一层！到目前为止，我一直假设所有的 CSS 都包含在某个样式表中。但是，在 React 中，您可以选择直接在 JSX 代码中使用样式。如果你不熟悉 JSX，我建议你回去读一下我在顶部链接的 Javascript layers。

由于 React 中的组件被设计为在整个应用程序中可重用，这允许您将您的样式与它们要应用到的代码段放在一起。

```
let styles = { margin: 0, flex: 1 };

return (
    <h1 style={styles}>
        LAURIE             
    </h1>
)} 
```

Enter fullscreen mode Exit fullscreen mode

> 老实说，我不喜欢内嵌样式。这样做感觉类似于 HTML 中的内联样式，这是令人不快的。但是不知何故，在 JSX 的土地上这是可以接受的，因为...原因？我想当你的 HTML 和你的 javascript 内嵌在一起时，在那一点上，一切都是耸耸肩。

请记住，即使这些样式没有直接写在 return 语句中，它们仍然被认为是“内联的”。这意味着 CSS 将这些样式视为比样式表中的 CSS 更具体。

## 还是风格化的组件，为什么不保持这种趣味性呢？

在这个过程中，人们决定创建样式化的组件。由于 JSX 的语法，这可能会有点混乱。然而，有一个根本的区别。

添加样式并将其与 HTML 标签相关联不是一个样式化的组件。样式化组件是专门的 React 组件，无论走到哪里，它们都带有自己的样式。

```
import styled from 'styled-components';
const Title = styled.h1`
  font-size: 1em;
  color: blue;
`;
Laurie 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，你可以看到没有必要为组件设计和分配一个类。它是直接完成的，不需要映射。这可能是一个完整的独立帖子，所以如果你有兴趣了解更多信息，请点击这里。

## 不是我的风格重访

除了包含 Bootstrap 之类的用于样式化的东西之外，您还应该注意第三方 Javascript 库中“意外”包含的样式。这可以通过多种方式发挥作用，但盖茨比形象就是一个例子。

```
<Img className="selfie" sizes={{...node.image.childImageSharp.fluid, aspectRatio: 4/3}} alt={node.conference} /> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码使用了 gatsby-image 插件中的一个`Img`标签。当它呈现时，它将包含代码片段中没有指定的其他样式和类。

## 当心流氓 javascript！

现在，这里有一个扳手扔进东西。样式是静态的...但是 javascript 可以动态地改变它们。

我最近试图修复我的个人网站中的一些响应性设计问题。它以前是用 Jekyll 编写的，运行良好，但当我将 CSS 移植到 Gatsby 项目时，它不再为较小的屏幕调整组件大小。当四处搜索时，我意识到 Jekyll 站点正在使用一个 init.js 文件来触发我的导航条切换。它只是坐在那里，当我的项目加载时，在我不知情的情况下，与风格鬼混！当然，这是为了正义，而不是邪恶，但仍然！

所以只要记住，当你的站点在使用时，样式是可以动态改变的。

## 我们现在看起来不漂亮吗！

[![messy kitchen from baking](img/a998d9844115b0b29a9bae2e2ac7f8fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2qi6UKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://media.tumblr.com/1b0659a74614aa6f130b7629d2a12cbd/tumblr_inline_mxhne4DHSw1qgeygd.jpg)

如果你[弄得一团糟](https://theoatmeal.com/comics/design_hell)我很抱歉，但至少蛋糕尝起来还是很好吃的！

* * *

如果你正在寻找类似这样的其他内容，请查看:
[不要被错误所迷惑](https://dev.to/laurieontech/don-t-get-fooled-by-errors-314c)
[在线学习技巧和窍门](https://dev.to/laurieontech/online-learning-tips-and-tricks-1lgi)
[我的关于 Gatsby 和 GraphQL 的系列文章](https://dev.to/laurieontech/rewriting-a-static-website-using-gatsby-and-graphql---part-1-53o0)