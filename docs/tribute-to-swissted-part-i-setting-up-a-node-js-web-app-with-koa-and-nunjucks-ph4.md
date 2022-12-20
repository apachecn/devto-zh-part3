# 向 Swissted 致敬—第一部分:使用 Koa 和 Nunjucks 设置 Node.js Web 应用程序

> 原文：<https://dev.to/ekafyi/tribute-to-swissted-part-i-setting-up-a-node-js-web-app-with-koa-and-nunjucks-ph4>

在本系列的[的第一部分中，我将创建一个*基本节点 web 应用程序，它使用服务器端数据*呈现 HTML 模板文件。在这个阶段，我们还没有处理实际的网站内容。](https://dev.to/ekafyi/tribute-to-swissted-introduction-2c6i)

下面的应用程序是我们将要构建的，我将很快一步一步地描述它。

[https://glitch.com/embed/#!/embed/eka-nodejs-koa-starter?path=index.html](https://glitch.com/embed/#!/embed/eka-nodejs-koa-starter?path=index.html)

**图书馆**

*   [Koa](https://koajs.com/)
*   Koa middleware: [koa-static](https://github.com/koajs/static) , [koa-router](https://github.com/ZijianHe/koa-router) , [koa-views](https://github.com/queckezz/koa-views)
*   [双节棍](https://mozilla.github.io/nunjucks/)

**目录**

1.  创建新的节点应用程序
2.  Install Koa
3.  安装和设置 Koa 中间件和 Nunjucks
4.  显示我们的索引页

我们开始吧！

* * *

## 1。创建新的节点应用程序

> 关于在 Glitch 上设置节点应用程序的详细演练，请查看启发了这个项目的京的[“渐进增强的练习”。](https://dev.to/huijing/an-exercise-in-progressive-enhancement-lk1)

我将描述如何在[毛刺](https://glitch.com)上做到这一点。如果您使用不同的环境，请跳到步骤 2。

创建一个账户或者登录 Glitch，然后点击右上角的**新项目**，选择`hello-express`。它将自动✨创建一个节点项目，并带你到编辑器视图。该项目使用一个随机自动生成的名称，您可以通过单击左上角的名称来更改它。

[![Glitch editor view showing new hello-express project](img/2afea8b4a0e58271f8efebc8026748f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QNoZsohA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0fugjsrk33swdgtmem0o.png)

我们正在删除 Express，因为我们正在使用 Koa，一个不同的库。为此，点击左下角的**工具**并打开**控制台**，它就像你机器上的命令行界面。

💡**提示:**点击**控制台**以全屏视图打开它(在一个新标签页中)，而点击**日志**在编辑器底部打开一个分屏，在这里你可以看到*日志*、*调试器*和*控制台*。

[![Glitch editor with Console split-screen at the bottom](img/592063e2d4391e1ff4ff748380d385ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b-QXjZp---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z9tgn0wsudurswy8bgii.png)

在控制台中运行此命令卸载 Express。(Glitch 使用`pnpm`包管理器而不是`npm`。)

```
pnpm uninstall express --save 
```

## 2\. Install Koa

现在我们正在安装 Koa，这是一个用于 Node 的现代 web 框架。你可以在他们的官方文件中了解更多信息。

要安装 Koa 并重新加载您的应用程序，请在控制台中运行以下命令:

```
pnpm install koa --save
refresh 
```

不过，我们仍然有 Glitch 的默认 Express 服务器代码，这会导致一个错误。让我们通过删除所有内容来修复它(😱)并将其替换为下面的代码。

```
// server.js

// initialize Koa app
const Koa = require('koa')
const port = process.env.PORT || 3000
const app = new Koa()

app.use(async ctx => {
  ctx.body = 'Hello new Koa app!'
})

const listener = app.listen(port, function() {
  console.log('Your app is listening on port ' + listener.address().port)
}) 
```

错误应该消失了，您应该会再次看到*“您的应用程序正在侦听端口 3000”*消息。你可以点击顶栏上的 **🕶秀**看现场，上面印着文字*“hello new KOA app！”*

[![Glitch Editor screen with Koa server code](img/ffbe32a0c3d66c7ca4a89e335bab76ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z03us9D---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ln9xj3eq2soa3adpjjpw.png)

## 3。安装和设置 Koa 中间件和 Nunjucks

Koa 通过将功能分成*中间件*来保持其核心框架的精简，我们可以根据应用程序的需求来安装这些中间件。

**中间件**是一种软件，*“促进客户端-服务器连接，在您的应用程序堆栈中形成一个中间层，充当应用程序和网络之间的粘合剂”* ( [Upwork](https://www.upwork.com/hiring/development/koa-js-a-future-proof-javascript-middleware-framework/) )。本文中的[将节点中的中间件定义为*“接收请求和响应对象的功能”*。](https://hackernoon.com/middleware-the-core-of-node-js-apps-ab01fee39200)

我们通过在控制台中运行下面的命令来安装三个 Koa 中间件包和 Nunjucks 模板引擎。

```
pnpm install koa-static koa-router koa-views nunjucks --save 
```

### 3a)配置 KOA-静态

我们使用 [koa-static](https://github.com/koajs/static) 来服务静态资产，比如 CSS 和客户端 JS 文件。加载`koa-static`并将`public`定义为我们的静态资产文件夹，方法是在`new Koa()`行后添加以下代码。

```
// server.js

// const Koa = require('koa') ... etc

// serve static assets with koa-static
const serve = require('koa-static')
app.use(serve('./public')) 
```

### 3b)配置 koa 路由器

顾名思义， [koa-router](https://github.com/ZijianHe/koa-router) 在我们的应用中负责路由。我们的用法与他们文档中的[“基本用法”示例](https://github.com/ZijianHe/koa-router#router-)相同。

在这里，我们加载`koa-router`，创建一个路由器实例，然后使用`router.get`处理 GET 请求——在本例中是到主/根路径(`/`)。

```
// server.js

// const Koa = require('koa') ... etc
// const serve = require('koa-static') ... etc

// initialize routing with koa-router
const Router = require('koa-router')
const router = new Router()

// ! remove these three lines
// app.use(async ctx => {
//   ctx.body = 'Hello new Koa app!'
// })

// ! add the lines below
// GET request for root
router.get('/', (ctx, next) => {
  ctx.body = 'Hello new Koa app with routing!'
})

// Add the given middleware function to this app
app
  .use(router.routes())
  .use(router.allowedMethods());

// const listener ... etc 
```

再次点击顶部栏上的 **🕶秀**观看现场直播。它现在应该打印*“你好，带路由的新 Koa 应用程序！”*

如果我们想呈现一个 HTML 文件，而不是像刚才那样返回文本，该怎么办？继续下一步。

### 3c)配置 koa-views 和 Nunjucks

我们使用 [koa 视图](https://github.com/queckezz/koa-views)来渲染我们的视图文件。术语“视图”指的是 web 应用程序中的*表示*或*用户界面*模板，在本例中是我们的 HTML 文件。

在这里，我们加载`koa-views`，定义我们的视图目录(`/views`),其中`koa-views`将查找我们的文件，并将 Nunjucks 定义为我们的模板引擎。Nunjucks 是一个 Javascript 模板引擎，我们将在 HTML 文件中使用它。它具有条件和循环等特性，这将有助于我们显示数据。

```
// server.js

// const Koa = require('koa') ... etc
// const serve = require('koa-static') ... etc
// const Router = require('koa-router') ... etc

// intialize view rendering with koa-views
const views = require('koa-views')

// define HTML templating engine
app.use(views('./views', { map: { html: 'nunjucks' }})) 
```

之后，让我们修改上一步 GET 请求中的函数，使*呈现我们的 HTML 文件*。用一个`ctx.render`函数替换`ctx.body`行，比如:

```
// server.js

// GET request
router.get('/', (ctx, next) => {
  // ! remove this line
  // ctx.body = 'Hello new Koa app with routing!'

  // ! add this line
  return ctx.render('./index')
}) 
```

现在，当用户访问根路径(`/`)时，我们的应用程序将呈现文件`views/index.html`。让我们在下一步继续努力。

## 4。显示我们的索引页面

🍎**注意:**如果使用 Glitch，应该已经有默认的 HTML 和 CSS 文件，`views/index.html`和`public/style.css`。如果您使用不同的环境，请在继续之前创建这些文件。

### 4a)准备我们的 HTML 和 CSS 文件

将下面的标题和正文添加到文件的`<body>`中。如果使用 Glitch，可以让`<head>`内容和页脚保持原样；只需替换正文内容。确保你链接到文档`<head>`中的 CSS 文件。

```
<!-- views/index.html -->
<html lang="en">
  <head>
    My New Node App
    <link rel="stylesheet" href="/style.css">
  </head>
  <body>
    <h1>Hello</h1>
    <p>Have a good day!</p>
    <!-- Include Glitch's default footer and button -->
  </body>
</html> 
```

接下来，我们将编写我们的 CSS 文件。你可以随心所欲地设计你的页面。

```
/** public/style.css */
h1 {
  font-style: italic;
  color: #373fff;
} 
```

重新加载您的页面，您应该看到您的新页面。

[![A page containing the title 'Hello' and body text 'Have a good day'](img/9dc4b79405fbbc29fc221949b8b226e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2swAwRq5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sohi7ajqa9b6mqmjn0w8.png)

### 4b)用数据渲染视图

现在我们将数据从服务器传递到我们的视图。第一步是从`server.js`获取数据并将其传递给模板文件。我们正在硬编码数据`name: 'Eka'`(请随意使用您自己的名字！)并通过向`ctx.render`函数传递一个对象将其发送到我们的 HTML 模板。

```
// server.js

// GET request
router.get('/', (ctx, next) => {
  return ctx.render('./index', { 
    name: 'Eka', 
  })
}) 
```

下一步是显示模板文件中的数据。打开`index.html`并更改标题文本以显示`name`。

```
<!-- views/index.html -->
<html lang="en">
  <head>
    My New Node App
    <link rel="stylesheet" href="/style.css">
  </head>
  <body>
    <h1>
      Hello {{ name }}
    </h1>
    <p>Have a good day!</p>
    <!-- Include Glitch's default footer and button -->
  </body>
</html> 
```

重新加载您的页面，您可以看到标题文本现在显示您的姓名。

[![Same page as above, but with the title saying "Hello Eka" instead of "Hello"](img/784996f717e81fe338439c59b28dd6ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yly-kcsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uaohd0pxg3haitao0bn6.png)

我们制作了一个节点 web 应用程序，它使用从服务器传来的数据呈现一个 HTML 文件！🎉

* * *

## 📝我的笔记

在经历了本帖中的步骤后我所学到的:*(切记这些都是我的主观看法！)*

*   Glitch 非常有助于您开始构建 Node.js web 应用程序。它消除了必须建立环境的障碍；而且它的设计方式让你可以很容易地从你的网络浏览器开发*。😻*
*   Node.js 对于前端的学习曲线似乎相当低。基本的 Javascript 知识应该足以让您入门。公平地说，我从这篇文章的[中了解到应该安装哪些包，否则可能不知道从哪里开始。但是一旦我了解了这些包并浏览了它们的网站或存储库，它们通常都有详细的文档，这将使我能够超越我所学的帖子。](https://dev.to/huijing/an-exercise-in-progressive-enhancement-lk1)

* * *

在下一篇文章中，我们将使用 Cheerio 从 Swissted 和 Codepen 网站“抓取”并解析数据。

快乐大厦！