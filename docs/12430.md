# GatsbyJS 和 WordPress 入门

> 原文：<https://dev.to/geocine/getting-started-with-gatsbyjs-and-wordpress-1k2k>

你知道如何用 HTML、CSS 和 JavaScript 创建一个静态网站吗？我相信你曾经使用过 WordPress 来创建你自己的博客。

你可以使用 WordPress，而不知道如何创建你自己的主题和插件。在野外有很多。但是一旦你开始按照你想要的方式修补东西，事情就变得复杂了。

如果有一种方法可以把 WordPress 变成一个 API 或者一个数据源，你可以从中提取内容并在任何地方显示它，那会怎么样？

在这篇文章中，我们将在 Headless 模式下设置 WordPress，创建一个基本的 HTML/CSS 模板，并使用 GatsbyJS 将我们的模板与 WordPress 集成。

# 文字出版社

我假设你知道如何安装 WordPress。如果没有，这里是[一步一步的指导](https://www.digitalocean.com/community/tutorials/how-to-install-WordPress-with-lamp-on-ubuntu-18-04)。

要在*无头*模式下设置 WordPress，我们需要安装以下插件:

*   WP API 菜单 -这将把菜单公开为一个 API。内置的 WordPress API 不公开菜单。
*   [高级定制字段](https://WordPress.org/plugins/advanced-custom-fields/) -我们将能够创建基于规则的定制字段，并能够通过 API 访问它
*   自定义帖子类型 UI -有时页面和帖子是不够的，我们想创建自定义帖子，如产品、电影等。
*   Postlight Headless 主题 -这个主题将删除面向客户端的网页，并用 JSON 替换它。

在 Headless 模式下运行意味着我们将仍然使用 WordPress Dashboard 来管理内容，但是我们将通过 API 来提供服务。因此，我们不会使用 WordPress 向我们的访问者显示生成的内容。

确保你的 WordPress 站点上至少有 1 篇文章和 1 个页面。确保至少有一篇文章有至少一个标签。我们将在后半部分使用它。

# HTML/CSS 模板

现在我们有了一个 WordPress 设置。让我们开始创建我们网站的基础模板。我将在鳄鱼网站使用[这篇文章中的*圣杯*布局](https://alligator.io/css/css-grid-holy-grail-layout/)

> 圣杯是一种在网络上很常见的布局模式。它由一个标题、一个左侧带有固定宽度导航的主要内容区域、中间的内容和右侧的固定宽度侧边栏以及一个页脚组成。

这是我在 Codepen.io 上构建的演示。我们将使用它作为我们在 GatsbyJS 上的布局。

[https://codepen.io/geocine/embed/wNVbeW?height=600&default-tab=result&embed-version=2](https://codepen.io/geocine/embed/wNVbeW?height=600&default-tab=result&embed-version=2)

# 盖茨比

如果您正在遵循本指南，我假设您的机器上已经安装了 NodeJS。如果没有，这里的[是一步一步的指导](https://www.taniarascia.com/how-to-install-and-use-node-js-and-npm-mac-and-windows/)。

GatsbyJS 是 ReactJS 的引擎盖，所以如果您对 React 或前端框架有基本的了解，那么遵循它会更容易。这里有一个很好的开端，让你了解由*马克西米利安·施瓦兹米勒*创作的。不要担心，你应该仍然能够遵循这个指南。

## 设置盖茨比

我们将使用 [gatsby-starter-wordpress](https://www.gatsbyjs.org/starters/GatsbyCentral/gatsby-starter-wordpress/) 。这里的`gatsbyhg`是我们项目的名字，你可以使用任何你想要的名字。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [盖茨比中央](https://github.com/GatsbyCentral) / [盖茨比-starter-wordpress](https://github.com/GatsbyCentral/gatsby-starter-wordpress)

### 寻找维护者- - -一个利用 WordPress API、ACF 等的 GatsbyJS 入门模板

<article class="markdown-body entry-content container-lg" itemprop="text">

# Gatsby v2 WordPress Starter

这个启动器是从[Gatsby-starter-netlify-cms](https://github.com/netlify-templates/gatsby-starter-netlify-cms)派生出来的，并修改为使用 WordPress 而不是 netlify-CMS，使用 [gatsby-source-wordpress](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-source-wordpress) 插件作为数据连接器。

演示:[https://gatsby-starter-wordpress.netlify.com/](https://gatsby-starter-wordpress.netlify.com/)

> **寻找维护人员**:如果你有兴趣接手这个启动器的维护工作，请联系我们。我们不再和盖茨比合作了(事情变得太复杂了，你好，nextjs)，所以我们不再维持这个了。

## 现在就使用它

```
gatsby new NAME https://github.com/GatsbyCentral/gatsby-starter-wordpress 
```

*   编辑`gatsby-config.js`，更改`baseUrl`
    *   确保你的 WordPress 站点上至少有一篇文章和一个页面
    *   确保至少有一篇文章有至少一个标签
*   确保你的 WordPress 安装中的永久链接结构设置为`Post Name`而不是默认的`Plain`，否则`gatsby-source-wordpress`插件将无法与 WordPress 通信
*   高兴
    *   有关源代码插件的更多信息，请查看…

</article>

[View on GitHub](https://github.com/GatsbyCentral/gatsby-starter-wordpress)

```
npx gatsby new gatsbyhg https://github.com/GatsbyCentral/gatsby-starter-wordpress 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过运行
来预览这个 gatsby starter 模板上的内容

```
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

然后，导航至`http://localhost:8000/`

## 连接我们的 WordPress 网站

连接到我们的 WordPress 网站。打开`gatsby-config.js`文件。寻找`baseUrl`，修改它指向你的网站，不要有`https://`。启动项目，检查显示的内容是否确实来自你的 WordPress 站点。

```
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

## 集成我们的 HTML/CSS 模板

gatsby-starter-wordpress 已经有了一个模板。我们将用我们的*圣杯*布局替换它。

首先，让我们编辑`src/components/Layout.js`来匹配我们的布局。
我们将专门修改这部分:

```
 <div>
   <Helmet title="Home | Gatsby + WordPress" />
   <Navbar />
   <div>{children}</div>
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
 <div className="layout">
    <Helmet title="Home | Gatsby + WordPress" />
    <header>Header</header>

    <Navbar />

    <main>{children}</main>

    <aside>Sidebar</aside>

    <footer>Footer</footer>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们在`src/components/`下添加一个名为`style.css`的 CSS 文件，内容如下:

```
/* Layout */

.layout {
  display: grid;

  grid-template-areas:
    "header header header"
    "nav content side"
    "footer footer footer";

  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  grid-gap: 10px;

  height: 100vh;
}

header {
  grid-area: header;

  display: flex;
  justify-content: space-between;  
}

nav {
  grid-area: nav;
  margin-left: 0.5rem;
}

main {
  grid-area: content;
}

aside {
  grid-area: side;
  margin-right: 0.5rem;
}

footer {
  grid-area: footer;
}

/* Theme */
/* This is optional, you can use your own style for this */

body {
  padding: 0;
  margin: 0;
  font-family: -apple-system,system-ui,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;
}

header {
  font-size: 18px;
  color: #fff;
  min-height: 20px;
  background: #0a2b4e;
  padding: 20px;
}

nav,
aside {
  background: #666;
  padding: 10px;
  color: #fff;
}

footer {
  background: black;
  color: #fff;
  padding: 20px;
}

.navbar-item {
  display: inline-block;
  color: #fff;
  padding: 10px;
}

.navbar-item img {
  height: 20px;
}

.pagination {
  margin: 0;
  background: black;
}

.pagination a {
  color: #fff;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`src/components/Layout.js`上修改样式导入

```
import './style.css' // add this
import './style/all.sass' // remove this, we won't be needing this anymore 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里查看[演示](https://gatsbyhg.netlify.com/)。这里的源代码是。