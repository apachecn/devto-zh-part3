# Dojo 中的构建时呈现

> 原文：<https://dev.to/odoenet/build-time-rendering-in-dojo-i6e>

您可能使用过其他支持服务器端呈现的框架。它主要做的是渲染你的页面的 HTML，并把它传递给客户端，就像最初的 JavaScript 加载时的样子，然后你可以与它交互，JavaScript 的东西发挥它的*魔力*来制作一个很酷的交互应用程序。

Dojo 方法要简单得多。您可以在构建过程中创建页面，然后将页面上传到任何地方，而不是在服务器上呈现页面。

你可以在 github 页面上找到一些关于 [dojo/cli-build-app](https://github.com/dojo/cli-build-app#build-time-renderbtr-object) 的构建时渲染的信息。您可以通过使用 [dojo/cli](https://github.com/dojo/cli) 快速搭建一个应用程序并稍加修改来开始。

一旦你准备好你的模板应用程序，让我们做一些修改。在`src/index.html`中，我们需要做的第一件事是添加一个构建时渲染工具可以使用的根 div。

```
<!DOCTYPE html>
<html lang="en-us">
<head>
  dojo-btr
  <meta name="theme-color" content="#222127">
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
  <div id="root"></div> <!-- Add this element -->
</body>
</html> 
```

现在我们可以为构建时渲染设置配置了。需要注意的一点是，默认情况下，模板应用程序使用散列路由，这意味着路由看起来像`myapp/#about`。这将在您的构建中生成一个 index.html 文件，它将快速加载这些路线。如果你使用不同的[历史管理器](https://github.com/dojo/framework/tree/master/src/routing#router)，它将为每条路线创建一个*index.html*。

```
{  "build-app":  {  "build-time-render":  {  "root":  "root",  "paths":  [  "#home",  "#about",  "#profile"  ]  }  } 
```

注意，我在路径前面加了一个`#`，这样 BTR 就可以正确地生成页面。这个输出非常有趣。每条路由都以字符串的形式存储在一个数组中，当您在运行时更改路由时，它会根据需要加载该路由的 HTML。

这样做的好处是，您的 HTML 已经准备好了，JavaScript 部分只是做它们自己的事情，而不必对您的页面进行初始呈现。这是一种非常敏感的体验。您可以通过一些简单的配置从构建时渲染中获得很多好处，所以好好利用它吧！

你可以在我整理的示例应用程序中看到这个示例[这里](https://github.com/odoe/dojo-btr)。我也有它运行现场[在这里](https://learn-dojo-btr-demo.netlify.com)。

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！