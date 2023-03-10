# hafcaf -适合所有人的无框架水疗解决方案

> 原文：<https://dev.to/andrew565/hafcaf-the-no-framework-spa-solution-for-everyone-ckj>

想想你最近一次不得不学习一个新的前端框架。可能是 React，或者 Vue，也可能是你害怕学习它们中的任何一个，因为在你开始之前，你有太多的“东西”要学，要做。您知道 React 有 42 页的“指南”来帮助开发人员了解用 React 编程的“正确”方法吗？Vue 和 Angular 都有 31。然后是尺寸。应用程序代码的最终大小通常以兆字节来衡量，这意味着用户可以在屏幕上显示任何内容之前等待几秒钟。

甚至不要让我开始构建工具的生态系统。

所以我受够了不得不在我的头脑中携带这些庞大的框架及其所有细微差别，我决定必须有一个更好的方法。一种回到简单时代的方法，那时你不需要手边有一本手册就能完成最简单的任务。由于没有找到我要找的东西，我自己做了一个解决方案。

hafcaf 在缩小和压缩后的重量不到 1KB。它只有两页文档:一个教程和 API 规范。它只需要您学习三种方法:init、addRoute 和 updateRoute。只有一个(可选的)生命周期钩子:onRender。最重要的是，如果您不想要或不需要任何 JavaScript 片段，您可以丢弃它们，基于 CSS 的路由功能仍然可以工作。

hafcaf 确实让您拥有尽可能少的 JavaScript，同时提供足够的内容来减轻网站和 web 应用程序开发的痛苦。你可以在 GitHub 上查看[。](https://github.com/andrew565/hafcaf)

# 神奇是如何发生的

哈夫卡夫的灵感来自海登·皮克林的一篇文章。要点如下:hafcaf 使用基于 CSS 的路由，使用:target 伪类，而不是 JavaScript 驱动的 URL 重定向——通常需要服务器端指令将所有 URL 重定向到您的 index.html 文件。这个伪类使用 href="#link "语法自动响应指向内部 id 的锚标记。

别担心，这比听起来容易多了。这里有一个例子。给定这个难以置信的精简示例网页代码:

```
<main>
  <section id="section1">
    I'm the content for section one.
    <a href="#section2">Section Two</a>
    <a href="#home">Home</a>
  </section>
  <section id="section2">
    I'm the content for section two.
    <a href="#section1">Section One</a>
    <a href="#home">Home</a>
  </section>
  <section id="home">
    I'm the content for the Home page.
    <a href="#section1">Section One</a>
    <a href="#section2">Section Two</a>
  </section>
</main> 
```

使用 hafcaf，如果您将浏览器指向`yourdomain.com/#section2`，那么将显示第 2 部分的内容，而不是两个部分的内容。hafcaf 通过隐藏所有非目标内容来做到这一点，实际上一次只显示一页内容。默认情况下，hafcaf 显示最后一块内容；在上面的例子中，默认情况下会显示“home”。

到目前为止，我们根本不需要使用任何 JavaScript。如果静态页面加载满足了您的需求，并且提供了您所需要的一切，那么，无论如何，不要费心加载 JavaScript 文件！这就是这个库的全部意义:你可以在你的站点/应用中拥有尽可能少的 JavaScript，而不会牺牲 SPA 的速度和 UX 的优点。

# 一点点 JavaScript 就能帮上大忙

如果您不介意在代码库中使用一点 JavaScript，并且想要一些奇特的特性，比如动态和/或惰性页面加载，hafcaf 的 JavaScript 模块可以满足您的需求——仅此而已。

*   支持异步/动态页面加载
*   支持延迟加载(仅在用户请求查看内容时请求和加载内容)
*   导航菜单的自动填充和激活(例如，用. active 类设置菜单项)
*   添加了在页面加载时触发的 onRender 动作

有几种不同的方法来利用动态页面加载功能，但关键部分归结为两个 API 方法:addRoute 和 updateRoute。这些方法所做的事情可能是不言自明的:addRoute 向 hafcaf 的路由集合添加一个路由条目，updateRoute 修改内容和/或设置。

这个过程基本上是这样的:用页面的基本信息创建一个“页面对象”,使用 addRoute 函数向 hafcaf 注册它，然后以您喜欢的任何方式获取页面的内容(我推荐 fetch ),最后用页面的实际内容调用 updateRoute。

这可能看起来有很多步骤，但比你想象的要简单。hafcaf 旨在以一种非常优雅的方式处理“异步”内容，这将轻松地适应任何流或框架。从前面的例子开始，让我们添加一个名为 page 3 的新页面。

```
<!-- page3.html -->
<div id="page-three">
    <h1>Page 3</h3>
  <p>Even moar content.</p>
</div> 
```

请注意，没有额外的 DOCTYPE、head 或 body 部分，只有我们希望这个新页面包含的内容。这是因为我们要将它直接注入到现有的页面中，所以它不需要普通 HTML 文件的所有额外定义。

接下来，在您站点的主 JavaScript 文件中(如果您愿意，甚至可以在 index.html 中)，设置 hafcaf.addRoute()函数，并指定一个可选的 linkLabel 属性:

```
var exampleDynamicView = {
  id: "page-3",
  linkLabel: "Page 3"
};

hafcaf.addRoute(exampleDynamicView); 
```

这个 linkLabel 属性告诉 hafcaf 用我们指定的标签“第 3 页”向页面菜单添加一个新条目。如果您想以不同的方式定义菜单，或者如果您没有菜单，这个额外的属性是可选的。

既然您的页面已经有了占位符，那么就去获取实际的内容吧。我更喜欢使用 fetch()，所以我会这样做:

```
fetch("https://yourserver.it/pages/page3.html")
  .then(response => response.text())
  .then(innerHTML => ({ innerHTML, id: "page-3" }))
  .then(page => hafcaf.updateRoute(page)); 
```

注意，我们将响应作为文本处理，然后将该文本赋给新页面对象的 innerHTML 属性。如果我们已经有了这个内容——可能通过更早地获取它——我们可以将这个相同的页面对象传递给 addRoute()，而不是 update route()；他们的工作非常相似。

# 还有什么？！？

hafcaf 还有更多的特性，包括 onRender()生命周期挂钩，可以用来设置事件处理程序(例如 onClick)或订阅流或 web 套接字——还包括 exitFunctions 集合，可以向其中添加任何处理器功能。通过几个类似插件的附件，您还可以访问 hafcaf Barista，它有助于自动化我在上面向您展示的获取和更新模式，以及 haf caf Tamper——一种简单的模板语言。很快，我计划添加 hafcaf Roaster 来处理查询参数(它们现在基本上被忽略了)，也是我最感兴趣的一个:haf caf Refill——Observer 模式的一个实现，它将利用 hafcaf Tamper 为您提供自动更新的视图；就像那些花哨的框架一样，但成本要低得多。

我和 hafcaf 的主要目标是给人们一个更简单的选择。当你必须开发复杂的应用程序时，hafcaf 并不是要取代复杂的框架。你不能用它来做 2D 侧滚。但对于大多数专注于显示很少或没有“互动”部分的内容的网站和应用程序来说，hafcaf 可能只是让事情变得更简单和容易。

在 Github 上查看[。](https://github.com/andrew565/hafcaf)