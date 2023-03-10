# 使用 Vue 和 Nuxt.js 进行服务器端渲染

> 原文：<https://dev.to/bnevilleoneill/server-side-rendering-with-vue-and-nuxt-js-3g22>

[![](img/c51cfbd641a094987dd96e1f768a33e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--liXIsqLu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AnYsBPEQCn4AJghTUO04frA.png)

服务器端渲染(SSR)或通用渲染，在 web 开发中并不是一个新概念。在单页面应用程序越来越流行之前，一个 web 页面在向服务器发出请求后，通常会收到一个 HTML(大多数情况下伴随着一些图像、样式表和 JavaScript)响应。然后在浏览器上呈现。

这在一段时间内非常有效，因为当时大多数网页主要只是显示静态图像和文本，几乎没有交互性。然而今天，这种情况不再存在，因为许多网站已经演变成成熟的应用程序，通常需要交互式用户界面。伴随这一需求而来的是使用 JavaScript 操作 DOM 的需要，这可能是乏味的，并且充满了许多低效，经常导致性能差和用户界面慢。

随着 React、Angular 和 Vue 等新 JavaScript 框架的引入，SSR 开始退居二线，这使得构建用户界面变得更快、更高效。这些框架引入了虚拟 DOM 的概念，其中用户界面的表示保存在内存中，并与真实 DOM 同步。此外，不是从 HTML 文档本身获得所有内容，而是获得一个带有 JavaScript 文件的基本 HTML 文档，该文件将向服务器发出请求，获得响应(很可能是 JSON)并生成适当的 HTML。这被称为**客户端渲染(CSR)。**

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

在 SSR 中，源 HTML 文件通常如下所示:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    Hello World
  </head>
  <body>
    <h1>My Website</h1>
    <p>Welcome to my new website</p>
    <p>This is some more content</p>
  </body>
</html> 
```

而在 CSR 中，当使用像 Vue 这样的 JavaScript 框架时，源文件会是这样的:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  Hello World
</head>
<body>
  <div id="root">
    <app></app>
  </div>
  <script src="https://vuejs.org"type="text/javascript"></script>
</body>
</html> 
```

正如您所看到的，HTML 标记中没有内容，而是有一个 id 为 root 的容器 div。在这个容器中，我们有一个特殊的标签 app，它将包含 Vue 解析的内容。服务器现在只负责加载网站或应用程序的最低限度。其他一切都由客户端 JavaScript 库处理，在本例中是 Vue。

### 利弊

每种方法的优点和缺点可以总结如下:

[![](img/fb9b3f34fdfd33899dd466d550692a2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OHcsfMfS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsEpxHoHVz5LQXnGeIyR_rQ.png)

### 为什么是 Nuxt.js

如前所述，CSR 或典型的单页应用程序的一个问题是 SEO，因为许多搜索引擎无法按预期抓取您的应用程序。虽然近年来谷歌的算法有所更新，可以更好地处理这些情况，但它还不是很完美。

我们如何在单页面应用程序中引入 SSR 的优势？Nuxt.js 是一个建立在 Vue 已经提供的 SSR 特性之上的框架，使得构建 SSR 应用程序更加容易。Nuxt 带来的优势包括:

*   SSR 有助于搜索引擎优化，加快初始页面加载
*   自动代码分割
*   静态文件服务
*   Vuex、Vue 路由器和 vue-meta 的智能默认值和预配置
*   为您的应用程序提供标准的文件夹结构
*   自动路由配置
*   模块化系统使得定制框架变得容易

### Nuxt 入门

要查看 Nuxt 的运行情况，首先，确保您安装了一个依赖管理器，如 [Yarn](https://yarnpkg.com/en/) 。在 Windows 上，这可以通过从 [Yarn 安装页面](https://yarnpkg.com/lang/en/docs/install/#windows-stable)下载并运行可执行文件来轻松安装。或者，你可以使用 [NPM](https://www.npmjs.com/) 。

让我们通过运行以下命令来搭建一个名为 nuxt-ssr 的新项目

安装几次后，您会看到一系列提示。由于这只是一篇关于 Nuxt 的介绍性文章，我们将选择最少的选项来保持简单:

*   对于*项目名称*和*项目描述*您可以选择默认值
*   对于*使用定制服务器框架*选择无
*   对于*选择要安装的功能*选择 Axios
*   对于*使用定制 UI 框架和使用定制测试框架*选择无
*   对于*选择渲染模式*选择通用(这是 SSR 的选项)
*   对于*作者姓名*,输入您的姓名
*   对于*选择一个卷装管理器*选择纱线

接下来进入 nuxt-ssr 目录，然后运行命令 yarn run dev:

[![](img/d57cad06b15dae6471af535b83f20b44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s1eRif99--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqjJB-7WAgIxReAwtAxWXsA.png)

在您的浏览器上访问 http:\localhost:3000，您应该看到类似这样的内容:

[![](img/abd7580a3a068d4d383f9b9604a883be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkMLVlBU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANss0Kau1Af_saYLOXa3bMw.png)

该内容是服务器端呈现的。如果您右键单击页面并查看页面源代码，您将能够看到 HTML 元素。例如，您应该能够在 body 标签中找到这个:

```
<h2 class="subtitle">
  My excellent Nuxt.js project
</h2> 
```

这意味着搜索引擎机器人将能够找到并索引这些内容。

记得之前提示中的一个问题要求我们*选择渲染模式。*现在，让我们看看如果我们选择了单页应用程序选项，会有什么不同。在 nuxt-SSL 目录中编辑文件 nuxt-config.js，将模式属性从 universal 更改为 spa:

```
import pkg from './package'
    export default {
      mode: 'spa',
      /*
      ** Headers of the page
      */
      head: {
        title: pkg.name,
        meta: [
          { charset: 'utf-8' },
          { name: 'viewport', content: 'width=device-width, initial-scale=1' },
          { hid: 'description', name: 'description', content: pkg.description }
        ],
        link: [
          { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
        ]
      },
      /*
      ** Customize the progress-bar color
      */
      loading: { color: '#fff' },
      /*
      ** Global CSS
      */
      css: [
      ],
      /*
      ** Plugins to load before mounting the App
      */
      plugins: [
      ],
      /*
      ** Nuxt.js modules
      */
      modules: [
        // Doc: https://axios.nuxtjs.org/usage
        '@nuxtjs/axios',
      ],
      /*
      ** Axios module configuration
      */
      axios: {
        // See https://github.com/nuxt-community/axios-module#options
      },
      /*
      ** Build configuration
      */
      build: {
        /*
        ** You can extend webpack config here
        */
        extend(config, ctx) {
        }
      }
    } 
```

然后在终端上按 Ctrl-c 停止应用程序。使用纱线运行开发再次运行应用程序。当您再次访问该页面并查看页面源代码时，您将无法找到我们之前在 body 部分中的内容。

让我们回到通用模式，并重新启动应用程序。

### 目录结构

让我们看看典型的 Nuxt 应用程序的目录结构。打开 nuxt-ssr 目录，您应该会看到这样的结构:

[![](img/dcf596db628a1ea9348ca52a9c37182b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pb8KxKGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/249/1%2A9n6ebe-E1taDR6Tb0RADOQ.png)

包含以下内容的目录。vue 文件是组件、布局和页面。组件目录包含我们可重用的 Vue 组件，布局目录顾名思义包含布局组件。在这个目录中，您会发现一个 default.vue 文件，这个文件是一个组件，但是包装了 nuxt 组件。当每个页面内容替换 nuxt 组件时，该文件中的所有内容在所有其他页面之间共享。

页面目录包含顶级视图，并为任何自动生成路线。这个目录下的 vue 文件。

在。存储目录我们存储用于状态管理的 Vuex 文件，静态目录包含我们希望完全按照原样提供的文件，例如 robots.txt 或 favicon。assets 目录包含我们未编译的资产——部署到生产环境时需要编译的东西，例如手写笔、SASS、图像和字体。在插件目录中，我们在启动 Vue 应用程序之前加载外部 JavaScript 插件。

在中间件目录中，我们放入了在呈现布局或页面之前运行的自定义函数。最后，我们有前面编辑过的 nuxt.config.js 文件，这个文件用于修改默认的 nuxt 配置。

### 导航组件

让我们创建一个简单的导航组件，它将在我们所有的页面上可见。在布局目录中创建一个名为 partials 的文件夹。在这个文件夹中，创建一个名为 nav.vue 的文件，并输入以下代码:

```
<template>
  <header>
    <nuxt-link to="/" class="logo">Nuxt-SSR</nuxt-link>
    <nav>
      <ul>
          <li><nuxt-link to="/">Home</nuxt-link></li>
          <li><nuxt-link to="about">About</nuxt-link></li>
          <li><nuxt-link to="services">Services</nuxt-link></li>
          <li><nuxt-link to="contact">Contact</nuxt-link></li>
      </ul>
    </nav>
  </header>
</template>
<script>
export default {
}
</script>
<style>
  header {
      background: rgb(0, 000, 000);
      display: grid;
      grid-template-columns: repeat(2,auto);
  }
  .logo, li a {
      padding: 1em 2em;
      display: block;
      text-transform: uppercase;
      text-decoration: none;
      font-weight: bold;
      color: white;
      font-size: .9em;
  }
  nav {
      justify-self: right;
  }
  ul {
      list-style-type: none;
  }
  li {
      display: inline;
  }
  li a {
      padding: 1em 2em;
      display: inline-block;
      background: rgba(0,0,0,0.1);
  }
</style> 
```

该组件显示了我们稍后将创建的页面的四个链接。注意，为了让 Nuxt 恰当地处理路由，我们没有使用

接下来，打开 layouts 目录中的 defaults.vue 文件，将其内容替换为:

```
<template>
  <div>
    <Nav />
    <nuxt />
  </div>
</template>
<script>
import Nav from './partials/nav';
export default {
  components: {
    Nav
  }
}
</script>
<style>
html {
  font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}
*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}
.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}
.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}
.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}
.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
</style> 
```

这里，我们在脚本部分导入了 nav 组件，然后在模板部分显示它。为了让我们的页面看起来更好，让我们包括一个字体。在 Nuxt 中包含字体有多种方法。一种方法是将其添加到 nuxt-config.js 文件中。在这个文件的 head 对象下，修改链接数组，如下所示:

```
link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
      { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css?family=Montserrat:300,700' },
    ] 
```

然后编辑靠近文件底部的 css 数组，如下:

```
css: [
    '@/assets/css/main.css'
  ], 
```

这是我们定义一个全局 css 文件地方，它将应用于我们所有的应用程序。然后，我们必须在 assets 目录中创建一个 css 文件夹和 main.css 文件。在该文件中输入以下内容:

```
body {
    font-family: 'Montserrat';
    margin: 0;
}
section {
    padding: 5em;
}
h1 {
    font-size: 3em;
    text-transform: uppercase;
}
.subheading {
    font-size: 1.5em;
    margin-bottom: 2em;
    text-transform: uppercase;
    color: rgb(179, 179, 179);
    font-weight: bold;
}
p {
    font-size: 1.2em;
    line-height: 1.4em;
}

  .page-enter-active {
    animation: bounce-in .8s;
  }
  .page-leave-active {
    animation: bounce-out .5s;
  }
  @keyframes bounce-in {
    0% { transform: scale(.9); opacity: 0; }
    100% { transform: scale(1); opacity: 1; }
  }
  @keyframes bounce-out {
    0% { transform: scale(1); opacity: 1; }
    100% { transform: scale(.9); opacity: 0; }
  } 
```

### 页面和路由

将 pages/index.vue 文件的内容替换为以下内容:

```
<template>
  <section class="container">
    <h1>The Best</h1>
    <p class="subheading">Hello world</p>
    <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Dolorem modi voluptate sequi excepturi natus, odio unde, neque voluptas, suscipit tempore dicta cupiditate sed nemo facilis ullam tenetur quidem? Dolore, expedita.</p>
  </section>
</template>
<script>
export default {
}
</script>
<style>
</style> 
```

重新启动应用程序，您应该会看到:

[![](img/40e16b26828d535fb85a276d54782eea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---yzBEPhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADyVNS7WzOlUgnD4jxb0sJg.png)

如前所述，Nuxt 中的路由是从页面目录中自动生成的。页面 index.vue 自动默认为归航路线。对于路由 about，我们只需在 pages 目录中创建一个 about.vue 文件。您可以对服务和联系进行同样的操作，您将看到路由正常工作！

### 结论

这是对 Nuxt.js 以及如何使用它来实现 SSR 的简要概述。Nuxt.js 有更多的特性，你可以在官方指南中找到。

如果你有任何问题或意见，不要犹豫，在下面张贴出来。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[用 Vue 和 Nuxt.js 进行服务器端渲染](https://blog.logrocket.com/server-side-rendering-with-vue-and-nuxt-js-64a6d99f5a82/)最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。