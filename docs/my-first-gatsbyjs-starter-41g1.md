# 我的第一个盖茨比首发

> 原文：<https://dev.to/niklasmtj/my-first-gatsbyjs-starter-41g1>

在自由代码营的博客上看到 Amber Wilkie ( [here](https://medium.freecodecamp.org/how-to-create-a-searchable-log-with-gatsby-d624bf3a05af) )关于一个使用我的主题的 Gatsby 网站的帖子后，我真的必须写一篇关于几周前我做的 GatsbyJS starter 的帖子。说实话，这是我发表的第一个个人项目。

我想了很久重新推出我的个人网站。我已经听说了很多关于盖茨比的事情，很想去看看。所以我直接跳进了[官方教程](https://www.gatsbyjs.org/tutorial/)和 [Scott Tolinski](https://www.youtube.com/watch?v=b2H7fWhQcdE) 的教程(这是 v1 的教程)并试图理解它。完成这些之后，我开始用盖茨比的默认启动程序从头开始构建我自己的页面，事实证明这是一个很好的开始。在完成该页面的第一个版本后，我想“为什么不作为盖茨比的开始来开发呢”。所以我们在这里。你可以马上开始。下面几行将描述你如何去做。

Gatsby 是一个使用 React 的静态站点生成器。这意味着 Gatsby 创建普通的 HTML 文件来提供服务。将 react 组件用于静态站点的想法很棒。它为简单的静态网页创造了很多机会，而且当你理解 react 的时候，它真的可以快速编码。值得看一看。

首先，页面将如何开始:[朱莉娅启动器](https://gatsby-starter-julia.netlify.com/)

## 特性

*   登陆和博客概述页面
    *   为了更好的区分一个干净的登陆页面和一个博客概述页面，我把博客放在了`/blog`
*   “降价采购来源/内容”文件夹
    *   因为我是 Markdown 的超级粉丝，所以我选择它作为我的首选
*   每个帖子的预计阅读时间
    *   我真的很喜欢这个关于媒体和许多其他博客的功能，所以这是我必须实现的东西
*   带情感的风格化组件
*   网络部署友好
*   Nunito 字体作为 npm 模块提供，可加快页面加载速度
*   受 Basecamp 启发的文本标记样式标题

## 快速启动

1.  创建一个盖茨比网站。

    使用 Gatsby CLI 创建一个新站点，指定 Julia starter。

```
 # create a new Gatsby site using the Julia starter
    gatsby new julia-starter https://github.com/niklasmtj/gatsby-starter-julia 
```

1.  **开始开发。**

导航到新站点的目录并启动它。

```
 cd julia-starter/
    gatsby develop 
```

1.  **打开源代码，开始编辑！**

您的站点现在运行于`http://localhost:8000`！

*注意:你还会看到第二个链接:`[http://localhost:8000/](http://localhost:8000/)T3】_*graph QL `。这是一个可以用来试验数据查询的工具。在 [Gatsby 教程](https://www.gatsbyjs.org/tutorial/part-five/#introducing-graphiql)中了解更多关于使用该工具的信息。**

在您选择的代码编辑器中打开`julia-starter`目录，编辑`src/pages/index.js`或`src/pages/blog.js`。保存您的更改，浏览器将实时更新！

1.  **创建自己的页面**

在`/pages`目录中创建新页面，如 About 页面。
最小代码为:

```
import React from "react"

import Layout from "../components/layout"
import SEO from "../components/seo"

const PageName = () => (
  <Layout>
    <SEO title="PageName" keywords={[`gatsby`, `application`, `react`]} />
    //CONTENT HERE
  </Layout> )

export default PageName 
```

## 那些都是什么文件？

为了更好地了解 GatsbyJS 使用的所有这些文件，请看一下 GitHub 上的自述文件。对所有使用的文件都有解释。

## 未来

这是一个巨大的工作进展中的项目，仍然有很多事情要做。我会不时尝试实现新功能。但这将取决于我在大学有多少空闲时间。

感谢您的阅读，
尼克拉斯

* * *

这篇文章首先发表在我的博客上: [niklasmtj.de](//niklasmtj.de/blog/my-first-gatsbyjs-starter)

布莱克·康纳利在 [Unsplash](//unsplash.com) 上的照片