# 服务器端渲染的优势和起源

> 原文：<https://dev.to/sunnysingh/the-benefits-and-origins-of-server-side-rendering-4doh>

> **注**:本文原写于[我的个人博客](https://sunnysingh.io/blog/server-side-rendering)。我在这里为开发者社区重新发布它。

服务器端渲染，缩写为 SSR，是一个经常被误解的概念。它是什么，你为什么要学它？我希望用一个关于服务器端渲染起源的故事来回答这些问题。

## 好处

在我进入 SSR 的故事和解释之前，有必要先了解一下它的好处。你为什么要在乎？

🔎搜索引擎优化:搜索引擎可以抓取内容，这样你的网站和页面就会出现在谷歌搜索结果中。

📣社交媒体优化:当人们在 Twitter、脸书等网站上发布你的链接时。然后一个漂亮的预览将会出现，包括页面标题、描述和图片。

🏎性能:服务器端呈现的页面将加载得更快，因为浏览器可以更快地获得内容。

😊用户体验:与性能类似，内容可以更快地获得，因此用户不必等待查看空白页或加载微调器。

我还做了一个[播客来解释 SSR](https://sunnycommutes.fm/episodes/58-benefits-of-server-side-rendering-8WXjwz) 的这些好处:

[https://www.youtube.com/embed/fP7Clzkwknc](https://www.youtube.com/embed/fP7Clzkwknc)

* * *

## 起源故事

自从 Java、PHP、Python 和 Ruby 等服务器编程语言出现以来，服务器端渲染就一直存在。如果你曾经在一个`index.php`文件或整个 Ruby on Rails 应用中编写过动态代码，那么你已经完成了服务器端渲染。

这使得理解这个概念变得简单多了。假设我有一个 PHP 网站，我从数据库中检索游戏列表。可能是这样的:

```
<?php

$game1 = getFirstGameFromDatabase();
$game2 = getSecondGameFromDatabase();

echo "<ul><li>$game1</li><li>$game2</li></ul>"; 
```

Enter fullscreen mode Exit fullscreen mode

数据在服务器上以 HTML 列表的形式被检索和格式化。当您在浏览器中查看这个页面时，您不必等待任何 JavaScript 运行。数据已经有了，你会马上看到游戏列表。

这对包括搜索引擎和社交媒体在内的所有人来说都是好事。数据已经存在于网页的源中，所以像谷歌甚至脸书这样的网络爬虫可以解析这些内容并显示搜索结果或链接预览。

几十年来，网站都是以这种方式建立的，但我们没有预见到的是，使用 JavaScript 在客户端编写网站的革命即将到来。

## JavaScript 革命

浏览器正变得越来越强大，这意味着与 10 年前相比，现在你可以用 JavaScript 做更多的事情。那么开发者开始做什么了呢？用客户端 JavaScript 编写他们的整个网站和网络应用。

是的，我主要指的是单页应用程序(SPA)框架的使用。虽然出现了许多，Angular 是普及这种方法的主要方法。想象一下，能够通过 Ajax 获取一些数据，在标记中添加一些特殊的属性，瞧，你就拥有了一个动态的网站，而不需要摆弄 PHP 和服务器。

但有一个大问题。您的初始 HTML 不再包含服务器已经在页面中很好地获取并返回给我们的所有数据。

现在你所拥有的就是这个:

```
<!-- 😭 My beautiful content is gone! -->
<div id="app"></div> 
```

Enter fullscreen mode Exit fullscreen mode

我敢肯定，谷歌对此很不高兴，用户也不高兴。在慢速连接时，JavaScript 可能需要一段时间才能使该页面有用。

*注意:在你说“但是 Google 现在可以抓取 JavaScript 了！”请记住，这是有限制的，并且不是所有的网络爬虫都做同样的事情。*

如果我说我们应该停止以这种方式构建应用程序，这将是一个非常可悲的结局，尤其是当它对开发者来说如此高效的时候。我们能鱼和熊掌兼得吗？

## 通用 JavaScript

现在一切都在这里了。如果我说我们可以采用传统的服务器端渲染方法，并将其与我们的 JavaScript 结合起来，会怎么样？

是的，有可能！这都要感谢 [Node.js](https://nodejs.org/) 允许所谓的通用 JavaScript:可以在浏览器和服务器上运行的代码。

假设我们有这样一个简单的 React 组件:

```
function GamesList({ game1, game2 }) {
  return <ul><li>{game1}</li><li>{game2}</li></ul>;
} 
```

Enter fullscreen mode Exit fullscreen mode

组件被渲染到页面上，如下所示:

```
const games = <GamesList game1="mario" game2="pacman" />;
ReactDOM.render(games, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

这些都是在客户端完成的。我们如何在服务器端做同样的事情呢？实际上，React 为此提供了一个方法:

```
return ReactDOMServer.renderToString(games); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以让 Node.js 服务器返回 React 组件的完整 HTML，而不是传回一个空的 div！类似于我们之前的 PHP 代码。

在这个例子中，我没有提到数据获取，但是请注意，在服务器端的组件中获取数据是完全可能的。

### 注意:你不会错过水疗的好处

单页面应用程序(SPA)之所以流行，不仅因为它提供了快速的开发时间，还因为它的客户端路由。这为最终用户提供了快速的导航体验，并且当我们开始服务器端渲染时，这绝对是我们不想失去的。幸运的是，您仍然可以选择在客户端使用这些框架来提供这种体验。这意味着**初始渲染使用 SSR** ，但是之后**后续导航就像 SPA** 。

* * *

## 在现实世界中使用它

我希望这个故事有助于解释什么是服务器端渲染，以及为什么你会想使用它。你可能想知道如何真正使用它。

虽然你可以从头开始，试着让你的应用程序在 Node.js 上运行，但这需要做大量的工作。您必须弄清楚如何正确地实现数据获取、状态水合、CSS 提取和许多其他事情。

谢天谢地，这是有框架的:

*   对于 React 项目，我强烈推荐 [Next.js](https://nextjs.org/) 。
*   对于 Vue.js 项目，看一下 [Nuxt.js](https://nuxtjs.org/) 。

获得服务器端呈现的好处而没有 Node.js 服务器的麻烦的另一个选择是使用静态站点生成器。当然也有局限性，比如不能拥有动态的随需应变路线(比如用户资料)，但除此之外，我绝对推荐看一看 [GatsbyJS](https://www.gatsbyjs.org/) 。我的个人网站由盖茨比提供支持，我也写了关于《T3》的《T2》。

我还应该提到预渲染，这基本上是拥有自己的网络爬虫，可以解析 JavaScript。然后，标记的结果被提供给适当的用户代理，如搜索引擎，这样做的好处是，您不必改变应用程序的编写方式。Prerender.io 是一个提供这种功能的流行服务。请记住，您仍然要么维护服务器，要么为服务付费，并且您不会从中获得任何性能优势。

哪种选择是最好的，没有明确的答案，你只需要权衡每种选择的利弊，决定哪种选择对你来说是值得的。