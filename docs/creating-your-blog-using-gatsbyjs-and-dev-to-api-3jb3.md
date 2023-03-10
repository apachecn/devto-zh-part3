# 使用 GatsbyJS 和 Dev.to API 创建您的博客

> 原文：<https://dev.to/geocine/creating-your-blog-using-gatsbyjs-and-dev-to-api-3jb3>

> TLDR:你可以去观看演示。尽管如此，这真的非常容易，即使你不知道盖茨比。

我在这里看过几篇 Dev.to API 的文章，决定用它做点有用的东西。

如果你已经有了博客，这篇文章也适合你。我一直在努力在我自己的域名上创建一些内容，因为我觉得没有人会费心访问我的网站。

不过在 [Dev.to](https://dev.to) 的时候，我只是继续回来写文章，因为我知道有人会以某种方式阅读它。

与其从我自己的博客交叉发布到 Dev.to，我想我可以反过来做。我正在介绍这个非常基本的 Gatsby 开发资源，你可以用在你的 Gatsby 网站上。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ geocine ](https://github.com/geocine) / [盖茨比-source-dev](https://github.com/geocine/gatsby-source-dev)

### 从 Dev.to 获取用户文章的 Gatsby 源代码插件

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![header](img/f2245c079649ff3685fa8a4b948b0a49.png)](https://github.com/geocine/gatsby-source-dev#readme) 
盖茨比-来源-发展

[![NPM version](img/d4e72e53ee1b5ad782c6f181c46d75c5.png) ](https://www.npmjs.com/package/gatsby-source-dev) [ ![Build Status](img/4008bac6bab8257eca171c043a4f98b8.png)](https://travis-ci.org/geocine/gatsby-source-dev)

一个 Gatsby 插件，从 Dev.to 的`/articles?username`结合`/articles/${id}`端点获取用户文章。

## 使用

在您的项目中安装`gatsby-source-dev`:

```
yarn add gatsby-source-dev
npm install gatsby-source-dev 
```

然后将插件添加到您的`gatsby-config.js`文件中:

```
{
  resolve: "gatsby-source-dev",
  options: {
    // This is your username on Dev.to
    username: ''
  }
}
```

Enter fullscreen mode Exit fullscreen mode

该插件将在 Gatsby 中存储 Dev.to API 响应。下面是一个获取文章`title`、`id`和`description`的查询示例。

```
{
  allDevArticles {
    edges {
      node {
        article {
          id
          title
          description
        }
      }
    }
  }
}
```

Enter fullscreen mode Exit fullscreen mode

该节点包含来自 Dev.to 端点的完整响应。

> 分页尚未实现

</article>

[View on GitHub](https://github.com/geocine/gatsby-source-dev)

如果你不知道什么是盖茨比的来源，或者从来没有真正接触过盖茨比，你不必担心，因为我创造了一个启动器。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [【地学】](https://github.com/geocine)/[【Gatsby-starter-owed】](https://github.com/geocine/gatsby-starter-devto)

### 利用 Dev.to API 的 GatsbyJS 入门模板

<article class="markdown-body entry-content container-lg" itemprop="text">

# 盖茨比启动器开发到

这是 [gatsby-source-dev](https://github.com/geocine/gatsby-source-dev) 插件的演示用法

还使用 [html-react-parser](https://www.npmjs.com/package/html-react-parser) 将 html 渲染为组件，如我在[的博客文章](https://dev.to/geocine/challenges-in-headless-wordpress-with-gatsbyjs-4a0b)中所述

</article>

[View on GitHub](https://github.com/geocine/gatsby-starter-devto)

以下是如何使用这个入门工具创建自己的博客的步骤:

1 -克隆 github 库

```
 git clone git@github.com:geocine/gatsby-starter-devto.git 
```

Enter fullscreen mode Exit fullscreen mode

2 -打开`gatsby-config.js`，用您的开发者用户名
更改第 13 行

```
 module.exports = {
      siteMetadata: {
        title: 'Gatsby + Dev.to Starter',
      },
      plugins: [
        ...
        {
          resolve: 'gatsby-source-dev',
          options: {
            // your Dev.to username
            username: 'geocine'
          },
        },
        ...
      ],
    } 
```

Enter fullscreen mode Exit fullscreen mode

3 -安装并启动

```
 yarn
     yarn start 
```

Enter fullscreen mode Exit fullscreen mode

4 -导航到 [http://localhost:8000](http://localhost:8000)

> [这个](https://gatsby-starter-devto.netlify.com/)应该是使用我的用户名`geocine`的样子。

这仍然是一项正在进行中的工作，我会添加更多的功能，随时创建一个拉请求。我希望你喜欢它。

如果你想更多地了解盖茨比，请查看下面这篇文章:

[![geocine](img/6ac7628f55df89ba009b8177bf6232c3.png)](/geocine) [## 一个动态静态站点生成器

### aivan mon celler 2 月 28 日 194 分钟阅读

#gatsby #graphql #react #jamstack](/geocine/a-static-site-generator-doesnt-have-to-be-static-41i)