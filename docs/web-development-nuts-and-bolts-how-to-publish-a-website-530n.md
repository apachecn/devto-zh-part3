# 网站开发的基本要素:如何发布网站

> 原文：<https://dev.to/kball/web-development-nuts-and-bolts-how-to-publish-a-website-530n>

这是一个做网页开发者的好时机。

有如此多的选择，如此多的框架，如此多的地方可以学习。

但是对于一个新的开发人员来说，要看到所有这些是如何组合在一起的是很棘手的。实际上，你是如何从你一直在学习的东西中获得人们可以在网上使用的东西的呢？

也许你一直在阅读 React 或 Vue 上所有这些很棒的文章。或者也许你一直在从 [freeCodeCamp](https://www.freecodecamp.org/) 学习 HTML 和 CSS。或者也许你一直在玩 WordPress 来建立你自己的博客…

你在电脑上用它工作，你就能让事情发生。但是你如何从在你的电脑上工作到一个每个人都能看到的网站呢？

*你实际上是如何将这个项目发布为网站的？*

别担心，你不是唯一感到困惑的人。让网络工作有很多不同的部分，要看到它们是如何组合在一起的并不容易。

这篇文章旨在填补这一空白:从在你的笔记本电脑上玩一个网站开始，你需要知道什么才能让它在互联网上直播。它是针对初学者的，所以如果你是专家，请随意滚动，如果你对任何事情感到困惑，请向下滚动并留下评论。没有问题是太基础的。

* * *

让我们从用户端开始——当你浏览网页时，实际上发生了什么？

## 浏览器

作为用户，你对网络的体验几乎完全是通过浏览器来实现的。

web 浏览器充当文档呈现器+交换机操作员的组合。

[![Simple diagram showing browser asking for documents, receiving them, and rendering them](img/f200c02f4bb9e53541a094333a06cd60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4NlN0bdt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zendev.com/assets/img/posts/how-web-applications-work/how-web-browsers-work.jpg)

### 文档渲染器

作为一个文档呈现器的模型，可以考虑像 Microsoft Word 这样的东西。Word 采用特定格式的文档(“word 文档”)，并允许您查看该文档并与之交互。

浏览器做完全相同的事情，但是它们理解的格式有三种主要格式:HTML、CSS 和 JavaScript。因此，它们不是解释“word 文档”，而是解释“HTML 文档”，以及一些相关的“CSS 文档”和“JavaScript 文档”，并让您查看它们并与之交互。

更进一步，浏览器所做的是将 HTML 文档翻译成一组称为“文档对象模型”节点或 DOM 节点的对象。这些构成了你将看到的和与之互动的基础。CSS 可以影响这些 DOM 节点在页面中的实际显示方式，JavaScript 可以根据其内部逻辑和用户交互动态地添加、删除或操作这些节点。

因此，作为文档呈现器，您的浏览器将一组文档转换成交互式页面或应用程序。

### 总机接线员

浏览器做的另一件事，也是让网络变得神奇的事，是它像总机接线员一样出去*寻找*它将呈现的确切文件。

当你在浏览器的地址栏中键入一个网址(又名 [URL](https://en.wikipedia.org/wiki/URL) )时(比如说 https://zendev.com/friday-frontend.html 的 T2，我的时事通讯的地址)，浏览器会把它翻译成一个请求，发送到运行在网络上某个地方的网络服务器(计算机)。它从域(zendev.com)中计算出将它发送到哪个服务器，并从协议和路径(分别是 https 和/friday-frontend.html)中计算出发送什么类型的请求。

这个请求最终会导致浏览器呈现一组新的文档，这就把我们带到了难题的下一个关键部分:什么是 web 服务器，它们是如何工作的。

## web 服务器

web 服务器是一个程序，它知道如何响应浏览器发送的请求类型(HTTP 或 HTTPS ),以及浏览器的内容(就我们目前的目的而言，HTML、CSS 或 JavaScript)。你可能听说过的一些常见的网络服务器有 [Apache](https://httpd.apache.org/) 和 [Nginx](https://www.nginx.com/) 。

当然，浏览器可以(也确实)要求其他东西……图像、视频和许多类型的数据格式……但是现在让我们把讨论限制在 HTML、CSS 和 JavaScript 上。

当一个请求进来时，web 服务器看到这个请求看起来像什么——嘿，有人在请求`/friday-frontend.html`，并且必须决定如何响应它。

### 静态内容

最简单的可能性是，它已经有了该页面的副本。这就是我的网站的工作方式，通常被称为“静态”网站。

静态意味着 web 服务器不做任何事情来改变基于请求的内容……它只是直接从请求映射到一个现有的文件，并将该文件作为响应传递。

注意到不同的文件可以被不同地处理也是有帮助的。大多数 CSS 和 JavaScript 文件都是静态提供的，这就是为什么它们通常被称为“静态资产”。

你写代码，也许使用类似于 [autoprefixer](https://github.com/postcss/autoprefixer) 或 JavaScript 构建系统提前做一些转换，但是当网络服务器知道它们的时候，它们只是文件，不会被改变。

### 动态内容

web 服务器用 HTML 响应的下一种方式是动态响应。这意味着没有文件确切地保存将要返回的 HTML 文档——相反，服务器将执行一些逻辑(通常从数据库或类似的地方查找数据),并使用这些逻辑来创建将要返回的 HTML 文档。

例如，如果我们正在创建一个用户资料页面，我们希望以一种可预测的方式定位这些页面，例如`/users/kball`和`/users/coolcat`。每个用户的个人资料将包含关于该用户的一些信息，可能存储在数据库中。我们不需要为`kball`和`coolcat`创建实际的 html 页面，我们可以使用一个动态服务器来识别表单`/users/[:username]`的所有路径，根据用户名从数据库中查找数据，并将数据呈现到一个模板中以创建最终的 HTML。

## JavaScript 框架呢？

如今，许多开发人员将 React.js、Angular 或 Vue.js 等 JavaScript 框架作为他们对 web 的第一次介绍。他们是如何融入的？

首先让我们退后一步，强调一下 JavaScript 在网络上为我们做了什么。

### JavaScript 的原始角色

JavaScript 是网络交互的主要语言。您可以使用 JavaScript 来改变和操作用户看到的内容，而不必再去服务器获取新内容。

想在用户点击时显示一个模态吗？您可能会使用 JavaScript 来做到这一点。想只更新页面的一部分而不加载整个新的部分吗？又是 JavaScript。

您甚至可以使用 JavaScript 直接从服务器获取新数据或页面，让您无需完全加载新页面就可以加载新内容。这种方法被广泛称为 AJAX，已经被越来越多的使用，因为它允许丝滑流畅的交互，减少了等待新页面的时间。

在极端的情况下，您会得到所谓的“单页应用程序”或“SPA”，其中您的初始 HTML 只是一个加载 JavaScript 的外壳，JavaScript 完成了设置页面、获取数据甚至在应用程序内的“页面”之间导航的所有繁重工作。

### 现代 JavaScript 框架

像 React 这样的现代 JavaScript 框架将这种趋势发挥到了极致。它们提供了非常强大的方法来使用 JavaScript 控制页面中的每一部分 HTML，并提供了非常开发人员友好的抽象和工具来轻松创建高级应用程序。

由于 JavaScript 现在也可以在服务器上运行，许多应用程序甚至会做所谓的“服务器端呈现”或“通用 JavaScript”，在这种情况下，您实际上构建了一个单页面应用程序，但它能够在服务器上(在第一次页面加载时)或在您的 web 浏览器中(用于后续交互)呈现它的所有部分

然后，这些 JavaScript 应用程序负责生成所有的 HTML，通常是应用程序中的所有 CSS。它们可以从 API 加载数据，或者只是生成静态页面的一种更好的方式。

### 这些 API 是什么？

随着越来越多的 web 开发转向 JavaScript，越来越多的数据和其他功能开始由 API 公开。有许多形式，但一个快速的思路是它们也是处理动态内容的 web 服务器(就像我们之前讨论的动态 HTML 一样)，但它们不是返回 HTML，而是返回一个不同形式的文档，该文档是为 JavaScript(或其他编程语言)设计的。这可能是一个 XML 文档，或者现在最常见的 JSON 文档。

这些 API“端点”(访问特定 API 的 URL 的别称)也可以处理各种不同的操作——从保存数据到处理信用卡，以及介于两者之间的任何事情。

像 Stripe(用于处理信用卡)或 Auth0(用户认证)这样的企业通常会将他们的产品完全公开为 API，然后为不同的编程语言(如 JavaScript)编写定制的“SDK”(软件开发工具包)，处理与这些 API 通信的所有细节。要开始使用它们，您只需在您的网站中包含它们的 SDK，添加一些特定于您的帐户的帐户信息，然后您就可以开始运行了。

## 拼凑

开始拼凑其工作原理的最后一件事是，每个 HTML 文档(以及较小程度上的 CSS 和 JavaScript 文档)都可以引用其他文档，然后浏览器将这些文档拉进来。

例如，一个 HTML 文档通常会引用一些它运行所需的 CSS 和 JavaScript 文档。当你的浏览器获取那个 HTML 页面时，当它开始解析它以呈现它时，它将看到那些依赖项并离开并获取它们。

[![Diagram showing browser asking for HTML, documents, receiving it, then requesting CSS and JavaScript, and finally rendering a page](img/5151f05c91c5986a1a7f152af692e42f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9_ar3vs2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zendev.com/assets/img/posts/how-web-applications-work/how-web-browsers-work-expanded.jpg)

在用 JavaScript 框架构建应用程序的情况下，可能会有一组额外的 AJAX 调用来获取更多数据并完成页面的构建。

一旦呈现出来，用户就可以与它进行交互，您就可以开始运行了。点击链接或提交表格将会以新的请求重新开始循环。

那么，这对于创建我们的 web 项目并让它们在 web 上可见意味着什么呢？

### 寻找服务器，又名托管

首先，这意味着无论你在构建什么，如果你想让用户可以加载，就需要有一个网络服务器。

很多情况下，公司会帮你处理这个。如果你在[WordPress.com](https://WordPress.com?aff=15938)或者 [SquareSpace](https://www.squarespace.com/) 或者类似的网站上托管一个网站，这些公司就是管理网络服务器的公司。

类似地，有无数的 WordPress 托管公司会让你“自我托管”,比 WordPress.com 这样的预包装解决方案给予更多的控制，但仍然会负责设置一个运行 WordPress 的服务器。我不是这方面的专家，但是 WordPress.org(这里的专家)推荐 [Bluehost](https://www.bluehost.com/track/kball/) 、 [Dreamhost](https://mbsy.co/spbfC) 和 [Siteground](https://www.siteground.com/index.htm?afcode=6f3d4b3838ae0007c8838665548cb800) 。

如果你试图找到一种方法来托管一个完全静态的网站，你的选择要广泛得多……你可以使用 [GitHub pages](https://pages.github.com/) 、[亚马逊 S3](https://aws.amazon.com/s3/) 、 [Netlify](https://www.netlify.com/) ，或者几乎任何其他可以让你在网上访问文件的服务。

如果你试图使用像 Express (JavaScript)、Django (python)或 Ruby on Rails 这样的动态 web 服务器来构建更加定制化或动态化的东西，事情就会变得更加复杂。

对于这些，也有为他们定制的托管解决方案，但你可能会考虑承担更高的技术负担或支付更昂贵的托管费用。或许最简单的起点是 Heroku，它会为你处理大量的技术负担/操作，但是随着你开始扩大规模，它会变得昂贵。

### DNS——域名的基本知识

我们没有谈到的一个关键问题是，浏览器实际上是如何*进行*地址查找的。当你输入`https://zendev.com/friday-frontend.html`时，它怎么知道`zendev.com`需要去*我的*服务器发出请求呢？

管理这个的系统叫做 DNS——我在这篇文章中不打算详细介绍它，但是从高层次上来说，你需要购买一个域名或者从别人的域名上下载。

抄袭别人的就好像我在`https://WordPress.com/zendev`或`https://zendev.squarespace.com`有一个网站。它通常与更具管理性的服务紧密相连，从品牌的角度来看肯定不是很好，但当你在建立你的网站时可能会很好。

要获得自己的域名，你需要通过域名注册商购买一个，并将其设置为指向你的网络服务器。

注册商方面，我强烈推荐 [Hover](https://hover.com/uqABLM9j) 。我已经把我所有的域名都转给了他们，甚至不再看其他的注册了。

就设置域名指向你的网络服务器而言，这往往是非常具体的你的主机，特别是当使用更多的托管服务，所以你应该看看你的主机提供商的文件。如果你有问题，请在下面给我留言，我会帮你指出正确的方向。

### 万事俱备

一旦你确定了你的网站或应用程序的托管位置，下一个问题就是你如何把你的文件放在合适的位置？

“老派”的做法是想办法“把文件放回原处就行”。这依赖于一个约定——文件需要放在哪里？这因主机提供商而异，但有一个“神奇的文件目录”是非常常见的，比如`/var/www`，文件将自动由网络服务器提供。

如果您的提供商遇到这种情况，通常他们会建议一种将文件上传到该位置的方法，如 FTP。即使是自己设置，一个“把文件放在适当的位置”的解决方案通常也是最简单的。对于我的网站[zendev.com](https://zendev.com)，我运行一个名为 nginx 的网络服务器，它只提供特定目录下的文件。当我进行更新时，我只需将文件放入该目录，更改就会显示出来。

今天，下一个最常见的推出代码的方式可能是源代码控制触发的部署。

“部署”一词用于描述一组事物的离散更新，即使您使用“将文件放在适当的位置”的方法，我也建议将更改分组到一个部署中，并在进行更新时进行跟踪。

Github 页面是从 git 存储库构建的，在那里您可以配置页面是应该从`master`分支、`gh-pages`分支还是主分支中的`/docs`文件夹提供服务。配置完成后，对上述分支的任何推送都将部署更新。

类似地，Heroku 上托管的应用程序基于对 git 存储库的推送进行部署。

其他部署方法包括打包 docker 映像等。

## 包装完毕

本文旨在让您全面了解 web 应用程序实际上是如何从开发环境变成人们可以访问和使用的东西的。如果一路上还有什么不清楚的地方，请在下面的评论中提出来。

* * *

如果前端开发是你关心的事情，你可能也会对我的每周时事通讯《星期五前端》感兴趣。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:[https://zendev.com/friday-frontend.html](https://zendev.com/friday-frontend.html)