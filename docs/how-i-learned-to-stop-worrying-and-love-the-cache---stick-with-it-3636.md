# 我是如何学会停止担忧并爱上它的

> 原文：<https://dev.to/shikaan/how-i-learned-to-stop-worrying-and-love-the-cache---stick-with-it-3636>

#### 查看更新版本[此处](https://withbenefits.dev/cache-fundamentals/)

# 简介

您可能已经注意到，这个子系列只是改变了名称:它与设计模式无关，没有必要将这两者作为同一事物的一部分。

正如在第一集中所说的[，这篇文章将更多地讨论在缓存方面已经存在的东西，以及我们作为开发者应该如何处理它。](https://dev.to/shikaan/-design-patterns-in-web-development----active-caching-1-23e2)

我们将要讨论的是你通常偶尔决定或者根本不决定的事情，因为一些框架和库为你做了这些决定。因此，这里的想法是有一个去处，在那里您可以理解或只是在每次再次处理缓存时掸掉这些概念。

## 提供正确的应用程序版本

提供 web 应用程序的正确版本开始成为你“最近”才应该关心的事情。

回到过去，我们有像 Java、JSP 和 PHP 这样的服务器端技术，这些技术曾经为瘦客户端应用程序提供很少的逻辑或者根本没有逻辑。随着时间的推移，客户越来越多，我们开始在前端和后端之间划分职责，前端和后端通常是两个完全不同的应用程序 [<sup>1</sup>](#note1) ，它们只是为了通信，而不是“同一件事”。

当应用程序由服务器运行时，提供正确的版本并不是一个问题，因为浏览器通常只是询问“那个页面”,并且在决定哪个版本的页面提供给 [<sup>2</sup>](#note2) 方面，决定权在服务器。

不幸的是，当应用程序位于客户端时，浏览器请求的页面通常是带有`<script>`的`index.html`，它通过`src`属性包含客户端应用程序。

所以如果`index.html`类似于

```
<!DOCTYPE html>
<html>
  <head>
    Wonderful WebApp
  </head>
  <body>
      <main id="app"></main>

      <script src="app.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

理论上，我们可以每次捆绑一个不同的`app.js`，而保持`index.html`不变。

不幸的是，现在不再是这样了。现在的浏览器知道某个东西是否改变了 [<sup>3</sup>](#note3) ，所以他们不会再问一遍`app.js`，他们只会假设它从未改变过，并继续使用旧的浏览器，除非我们告诉他们不要这样做。

一种方法是将应用程序的版本作为查询字符串参数添加到`src`中。

```
<!DOCTYPE html>
<html>
  <head>
    Wonderful WebApp
  </head>
  <body>
      <main id="app"></main>

      <script src="app.js?v=1.2.3"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

因此，每次我们推出新版本的包，浏览器都会被迫执行新的请求，因为 URL 和`index.html`发生了变化。

另一种类似的(目前更常见的)方法是用一个在每次部署中都不同的散列来命名包。散列可以基于实际的版本、代码、最新的修订号，甚至是构建发生时的时间戳。

```
<!DOCTYPE html>
<html>
  <head>
    Wonderful WebApp
  </head>
  <body>
      <main id="app"></main>

      <script src="app.gt8heef.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这种技术相当流行，在流行框架的 CLI(如 Create React App、Angular CLI、Vue CLI、Ember CLI)中大部分时间都是“免费”的。

您可以自己使用一个构建工具来实现它，该工具重写了包含版本号/散列的`index.html`，或者最终使用`manifest.json`来获得更细粒度的控制。帮助您实施的一些参考资料:

*   [Webpack - Caching](https://webpack.js.org/guides/caching/) 我的首选方式；
*   [中型解决浏览器缓存地狱与 Gulp-Rev](https://medium.com/@felipebernardes/solving-browser-cache-hell-with-gulp-rev-6349a293abb9) 温和介绍`manifest.json`；
*   [DZone -使用 Gulp 来捆绑、缩小和缓存-bust](https://dzone.com/articles/use-gulp-to-bundle-minify-and-cache-bust) 旧的，但仍然相关；
*   Jessie Wong -用 Makefile 破坏缓存对我来说有点难，但仍然是一个选择。

## 通过服务人员优化内容交付

在与`manifest.json`齐头并进的事情中(特别是关于进步的网络应用)，我们有服务人员。

不需要太详细地描述您可以用服务工作者 [<sup>4</sup>](#note4) 做的令人惊叹的事情，您可以将它们想象成一个在浏览器中并行运行的 JavaScript 线程，其生命周期不与客户端应用程序的生命周期绑定。

我们对它们如此感兴趣的原因是，在 Service Worker API 中，我们可以访问缓存接口。

您可以遵循一些缓存方法，但最常见的是:

*   安装时
*   关于用户交互
*   论网络响应

命名惯例是从你能在网上找到的关于这个问题的最伟大的资源之一，即[谷歌的离线食谱](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/)中借用的。

如果你关注了前几集发生的事情，你肯定会注意到服务人员在这些食谱中扮演的角色就是我们通常所说的*资源经理*。因此，在下面的段落中，我将展示这些模式如何映射到我们在以前的文章中所说的内容。

公平地说，关于这个话题还有另一个非常好的资源，那就是 [Mozilla 的服务工作者食谱——缓存策略](https://serviceworke.rs/caching-strategies.html)，但是我发现谷歌的观点更容易理解。我强烈建议你阅读这两本书，以便有一个更广泛的了解。

### 开机安装

在这个模式中，我们在服务工作者的`install`钩子上进行缓存写操作。当您希望存储应用程序外壳以提供离线体验时，它看起来特别有用。

在谷歌的食谱中，这以两种不同的方式出现，称为“作为依赖”和“不作为依赖”，基本上是“直写”和“后写”。

### 与用户互动

从缓存策略的角度来看，这种模式与`On Install`没有什么不同。

假设您想在博客上实现一个“稍后阅读”按钮。你需要做的是获取文章并存储它。决定是同步保存(如“直写”)还是异步保存(如“后写”)取决于您的用例，但这两种方法都是可行的。

### 对网络响应

在我们提供的三个例子中，这是最常见的，因为每次需要通过网络获取数据时，您都可以应用这种策略。

离线菜谱中提出的实现是“通读”——不多不少！

## W3C 标准:HTTP 头

在 web 开发的奇妙世界中，找到一种新的被欺骗的方式从来都不是问题。这就是为什么您可能想了解浏览器如何与服务器就缓存的内容进行通信。

> **免责声明**
> 尽管在下面的段落中我会一直提到浏览器，但这也适用于服务器到服务器的通信，所以后端用户也会觉得这很有趣。

同样，我只处理最有趣的案例，但是在这里您可以找到涵盖更多案例的资源列表:

*   [MDN - HTTP 缓存](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
*   [数字海洋-网络缓存基础知识](https://www.digitalocean.com/community/tutorials/web-caching-basics-terminology-http-headers-and-caching-strategies)
*   [key dn-HTTP 缓存头解释](https://www.keycdn.com/blog/http-cache-headers)
*   [W3C 缓存控制规范](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
*   [W3C - ETag 规范](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.19)
*   [谷歌 HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)

### ETag

尽管名称并不明确，但是 ETag HTTP 头是我们可以用来控制缓存内容的头之一。ETag 代表“实体标签”,它是一种用哈希来标记我们正在交换的内容的特定版本的方法。

在这种情况下，一个例子会胜过一千句话。

假设您作为一个客户端(另一个服务器或浏览器)正在请求`GET /dogs`。对方将用 200 和以下响应头进行响应:

```
200 OK
Content-length: 512
Cache-Control: max-age=60
ETag: a23g1t4 
```

Enter fullscreen mode Exit fullscreen mode

正如我们将在下面几节中看到的，`max-age=60`告诉我们内容将在 60 秒后变得陈旧。

假设一分钟后，我们再次请求相同的资源，但这次我们附加了以下请求头:

```
GET /dogs
If-None-Match: a23g1t4 
```

Enter fullscreen mode Exit fullscreen mode

这基本上意味着“给我有效的缓存内容或陈旧的内容，只要其版本是 a23g1t4”。

此时，服务器将尝试提供缓存的内容，然后退回到该版本的陈旧内容，如果没有找到，则执行实际的请求。如果找到缓存的内容，响应将是:

```
304 Not Modified
Content-length: 512
Cache-Control: max-age=60
ETag: a23g1t4 
```

Enter fullscreen mode Exit fullscreen mode

### 缓存-控制

Cache-Control HTTP 头用于从客户端(例如，“不要给我缓存的内容”)和服务器端(例如，“此内容将在两分钟后过期”)定义缓存策略。

Cache-Control 有*吨*的指令，这些指令可以以许多奇特的方式组合，这在几段文字中是不可能涵盖的。也许写一篇单独的文章是有意义的(如果你感兴趣，请让我知道！).这里我们将只讨论最常见的指令。

#### [T3`no-cache`T4】T1】](#-raw-nocache-endraw-amp-raw-nostore-endraw-)

这两个坏男孩可能是最混乱的。

指令`no-store`——在*请求头*和*响应头*中用作指令——仅仅意味着应该跳过任何缓存机制。因此，客户端不应该缓存它得到的响应(当在请求中使用时)，服务器也不应该缓存响应以加速后续请求(当在响应中使用时)。

当用作*响应头*时，`no-cache`意味着所提供的内容不会自动对后续请求有效。这基本上意味着内容可以被缓存或者不被缓存，但是，如果被缓存，它必须在被提供之前被验证(例如使用`ETag`)。

当用作*请求头*时，`no-cache`意味着我们不关心缓存了什么，我们想要一个新的请求。然而，这并没有定义服务器是否可以缓存响应以加速后续请求(与`no-store`相反)，通常服务器会缓存响应。

#### [T3`public`T4】T1】](#-raw-public-endraw-amp-raw-private-endraw-)

这些看起来很明显，但它们实际上隐藏了一个小怪癖。

大多数时候是没用的，事实上，你很少发现它。它只是意味着“这个内容可以被安全地缓存”，但是通常你有其他的指令告诉你(例如`max-age`，正如我们将要看到的)。

`private`反而更棘手一点。这并不意味着您根本不能缓存响应，而是说“只有当您拥有内容时，您才能缓存它”。

成为信息的所有者意味着什么？

假设您有一个微服务构建的应用程序，它前面有一个 API 网关。每个服务*和*API 网关都可以有缓存，但是只有微服务自己拥有信息。通常，标记为`private`的内容只针对一个特定用户，因此只有该用户和该信息的创建者可以缓存它。

因此，在上面的例子中，浏览器实际上可以缓存该信息(因为用户拥有该信息),产生该信息的微服务可以，但是 API 网关不能，以及两者之间的任何最终 CDN 也不能。

#### `max-age`

当在请求中使用时，`max-age=n`意味着客户端愿意接受不超过`n`秒的内容。

在响应中使用时，`max-age=m`表示传递的信息将在`m`秒后被视为过时。

# 最后的话

这是这个圣诞节的结束，但也许不是这个缓存系列的结束。谁知道呢？我们可以涵盖更多的内容...

像往常一样，如果你有任何反馈(例如，你为什么停止使用迷因？你为什么对缓存如此着迷？你怎么能在没有提到食物的情况下完成一整集呢？)随意伸手。

下次见！

* * *

[1](#note1ref) 。我们仍然有服务器端渲染的应用程序，但是*通常是*负责渲染客户端的后端层仍然没有照顾到业务逻辑的其他部分，使得整个事情在一定程度上仍然是分裂的。

[2](#note2ref) 。这并不完全正确:即使使用服务器呈现的应用程序，也有获得陈旧内容的方法。我们将在 [HTTP Headers 部分](#http-headers)中讨论如何在这种情况下(以及其他情况下)获取新内容。

[3](#note3ref) 。我们将在 [HTTP 头章节](#http-headers)中更深入地探讨浏览器实际上是如何理解何时请求新数据的

[4](#note4ref) 。我不会让你失望的❤ [这里](https://developers.google.com/web/fundamentals/primers/service-workers/)是谷歌在这个问题上的一个很好的介绍。