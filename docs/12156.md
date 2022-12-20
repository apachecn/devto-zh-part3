# 适用于所有开发人员的 React 教程系列(第 1 部分):简介！

> 原文：<https://dev.to/mathspy/accessible-react-tutorial-series-for-all-devs-part-1-intro-3k5>

你好。

本教程系列的简要介绍:React 是一个非常棒的 JavaScript 库，用于创建各种交互式网站。遗憾的是，许多开发人员都害怕它，因为它经常与许多其他新概念一起引入，而这些概念实际上与使用 React 并不相关或不需要！

因此，本系列旨在让任何具有基本 HTML/CSS/JS 知识的人都能接触到。它假设你对所有其他与 React 相关的概念没有任何了解(是的，如果你只知道 HTML/CSS/JS，你就可以了！)

这个系列的灵感很大程度上来自于另一个关于 Vue 的可访问系列 (Vue 是另一个与 React 非常相似的令人敬畏的框架)
如果你想学习它，请查看那个系列！

我们还在等什么？我们走吧！

要跟进，您可以打开任何本地文本编辑器(是的，包括记事本！).一个非常好的免费开源编辑器是 VS Code 。
**或者**如果你不想或者不能下载一个编辑器，你可以跟随[这个我事先准备好的在线代码编辑器！(简单开始编辑 index.html)](https://codesandbox.io/s/lpo383kko9)(旁注:Codesandbox.io 其实是用 React 做的🤭)

因此，我们开始(以及在可预见的将来)所需要的只是一个空白的 HTML 文件，其中包含一个用于 React 和 ReactDOM 的脚本引用，以及我们自己的用于编写代码的脚本标记。(如果你使用的是本地文本编辑器，把它保存在任何地方，然后用你最喜欢的浏览器打开)

```
<html>
  <head>
    <meta charset="UTF-8" />
    My First React App
  </head>

  <body>
    <div id="app"></div>

    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>

    <script>
      // Our code will be here!
    </script>
  </body>
</html> 
```

> 在这个系列中，我将包括这些不必要的“额外”部分(你可以完全忽略它们！)但会给出有趣的见解。
> 
> **EXTRA** :所以如果你曾经使用过其他库，比如 [jQuery](https://jquery.com/) 或者 [p5.js](https://p5js.org/) 你可能会奇怪为什么 React 需要两个脚本而不是一个？
> 我们可以把 React 想象成我们奇特的超级电池，我们可以把它放入许多不同的玩具中，让它们运转起来！那些玩具没有电池是完全没用的，但是电池可以和很多玩具兼容。在这种情况下，我们将要玩的玩具是 DOM(是`Document Object Model`的缩写，老实说，在这种情况下，它只是 HTML 的一种花哨说法)

所以让我们尽可能简单地开始吧！

```
<script>
  // Our code will be here!
  var app = document.getElementById("app");

  ReactDOM.render("Hello World!", app);
</script> 
```

(不要忘记保存更改并刷新页面)
你应该看到这个:
[![Hello World!](img/1aeb96de83fb2608988c84bb2c611495.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VS2gpTaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Mathspy/react-accessible-tutorials/master/part01/imgs/HelloWorld.png)

好吧，这是怎么回事？首先，我们要求浏览器给我们一个对我们的`<div id="app"></div>`的引用，我们把它保存在一个名为`app`的变量中。然后我们简单地问 React，“嘿，你能把“Hello World”渲染成这个 div 吗？”
反应过来高兴地回答道:

> 你的话就是我的命令！你从未有过像我这样的朋友！T3】

这很酷，但不是很令人兴奋，让我们尝试一些更令人兴奋的东西！

```
<script>
  // Our code will be here!
  var app = document.getElementById("app");

  var hello = React.createElement("h1", null, "Hello World!");

  ReactDOM.render(hello, app);
</script> 
```

现在我们要求 React 为我们创建一个<u>元素</u>(这是一个通用词，表示我们可以要求 React to `render`的任何东西)。元素可以是任何 HTML 标签*(或者其他我们稍后会研究的东西！)*)。在这种情况下，我们的元素将是一个“h1”(header 1)。嗯，那“T1”和“你好，世界”呢？在下一部分中，我们将更深入地探讨用什么来代替`null`！而是“你好世界！”就是 React 所说的`children`

```
<h1> <!-- parent -->
  <!-- everything here is a child of h1 -->
  <!-- children can be simple text/strings like "Hello World!" -->
</h1> <!-- end of parent --> 
```

所以我们基本上要求 React 为我们呈现一个简单的`<h1>Hello World!</h1>`

好吧，再举一个例子！这次我们来渲染一个列表！我明天需要去超市买土豆和西红柿，我想列一个清单，走吧！

在纯 HTML 中可能是这样的，对吗？

```
<ul>
  <li>Tomato</li>
  <li>Potato</li>
</ul> 
```

嗯...我想知道我们如何对这样的嵌套标签做出反应？嗯，元素的子元素不一定是纯文本！它们也可以是其他元素！

```
<script>
  // Our code will be here!
  var app = document.getElementById("app");

  var tomato = React.createElement("li", null, "Tomato");

  var list = React.createElement("ul", null, tomato);

  ReactDOM.render(list, app);
</script> 
```

[![Lonely Tomato](img/223ee83aacf7e4aa9c459b34b2aafd6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9OUhN_WI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Mathspy/react-accessible-tutorials/master/part01/imgs/LonelyTomato.png)

但是我们怎么能在一个元素中包含两个孩子呢？Well React 不介意你为一个元素传递任意数量的子元素！

```
<script>
  // Our code will be here!
  var app = document.getElementById("app");

  var tomato = React.createElement("li", null, "Tomato");
  var potato = React.createElement("li", null, "Potato");
  var regret = React.createElement("li", null, "Something I will buy and regret later 😭");

  var list = React.createElement("ul", null, tomato, potato, regret);

  ReactDOM.render(list, app);
</script> 
```

[https://codesandbox.io/embed/n5vpn53mwp?module=/index.html](https://codesandbox.io/embed/n5vpn53mwp?module=/index.html)

今天就到这里吧！我知道你现在可能会问自己“但这有什么意义呢？”你是对的，到目前为止，我们没有做任何令人兴奋或有趣的事情，你不能在更短的时间内用纯 HTML 做，但这将在下一部分中改变，当我们学习*道具和组件*！敬请期待！

有什么问题欢迎在评论里问我！