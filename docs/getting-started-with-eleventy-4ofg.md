# Eleventy 入门

> 原文：<https://dev.to/lauragift21/getting-started-with-eleventy-4ofg>

[![11ty](img/5cd5f7d31d8b2e815e061e60664dadb8.png "11ty")](https://res.cloudinary.com/practicaldev/image/fetch/s--5l8qH38O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547576269/gift-egwuenu/Screen%2520Shot%25202019-01-15%2520at%25207.13.40%2520PM.png)

由于网络不断发展的本质，这里那里总会出现一个时髦的词或新技术的出现，让我们作为开发人员对实际学习和掌握的东西感到敬畏。

在之前的一篇文章中，我谈到了 JamStack，以及它是网络上最棒的事情之一，也是你应该尝试它的原因。这篇文章将向你介绍一个静态站点生成器，叫做 _ [Eleventy](https://www.11ty.io/) ，也叫做 11ty。我们将看看如何开始使用它，并探索它的使用案例。

Eleventy 是一个更简单的静态站点生成器。-标记单词 simpler，说明这将是一件轻而易举的事。Eleventy 与 Jekyll 非常相似，但它不是用 Ruby 编写的，而是用 JavaScript 编写的，并且需要零配置设置，这有多有趣？就其他静态网站已经存在多久而言，它仍然是一个相当新的孩子。它于 2018 年初发布

Eleventy 使用起来非常灵活。它允许你选择一个模板来工作，你可以选择一个或者在一个项目中一起使用。它支持以下模板语言:

*   超文本标记语言
*   努恩朱克斯
*   EJS
*   降价
*   液体
*   把手
*   髭
*   Haml
*   哈巴狗
*   JavaScript 模板文字(ES2015)

使用 11ty 的一个好处是，目录中的每个模板文件都是一个独立的页面，这将导致代码库的巨大结构。

### 先决条件

本教程要求您拥有 Node.js 版本 8 或更高版本以及 JavaScript 的基础知识。

### 安装

```
// you can choose to install globally

  npm install -g @11ty/eleventy

// install locally within the project directory

  npm install --save-dev @11ty/eleventy 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 app

```
// initialize a new project and navigate to the directory

mkdir eleventy-site
cd eleventy-site

// run to start the app
eleventy 
```

Enter fullscreen mode Exit fullscreen mode

[![img](img/6d084bca1f39314d97ac5a905258d826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZtiOm2cH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547579929/gift-egwuenu/Screen%2520Shot%25202019-01-15%2520at%25208.18.16%2520PM.png)

在运行了`eleventy`命令之后，我们得到了上面的报告，因为我们目前没有任何文件供它处理。让我们给我们的网站添加更多的内容。

```
 // create a README.md file and run the app

  ### Eleventy Site tutorial

  > I'm just testing out its awesomeness 
```

Enter fullscreen mode Exit fullscreen mode

现在我们看到了输出中的实际差异，文件在`__site/_`文件夹中被处理成 HTML 文件。

[![img](img/0abab1b301ecbeb5bc0b1431cac9d833.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFK1cf_B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/lauragift/image/upload/v1547580896/gift-egwuenu/Screen%2520Shot%25202019-01-15%2520at%25208.33.30%2520PM.png)

使用静态站点的好处之一是提高效率和性能。注意上面输出中文件的处理速度。

### 布局

布局是可用于包装其他内容的模板。为了表示内容应该包装在模板中，我们简单地将布局关键字添加到前面的内容中。

```
<-- layout.md-->
 ---
layout: layout.njk
title: New Blog Post
--- 
# New Blog Post 
```

Enter fullscreen mode Exit fullscreen mode

该内容将被传入布局文件，如上所述

```
<-- _includes/layout.njk -->

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    {{title}}
  </head>
  <body>
    {{ content | safe }}
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这个模板的输出现在将由内容文件中的数据填充，如下所示。

```
<-- _site/layout/index.html -->

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    New Blog Post
  </head>
  <body>
    <h1>New Blog Post </h1>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 配置

有了 Eleventy，我们可以有一个符合我们需要的定制配置，这样您就可以通过创建一个`.eleventy.js`文件来覆盖默认的配置设置。我们可以通过修改默认的输入和输出目录来创建一个定制的配置来利用这一点，如下所示。

```
<-- .eleventy.js -->

module.exports = {
  dir: {
      input: "views",
        output: "dist"
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

使用自定义配置文件可以实现很多功能。这里有一些[配置选项](https://www.11ty.io/docs/config)的列表，您可以在配置文件中查找并添加到列表中。

### 消耗数据

Eleventy 可以在一个模板上使用不同来源的数据。它从数据源填充数据的顺序从最高优先级到最低优先级。这些是根据那个范围的来源。

*   [模板中的前置事项数据](https://www.11ty.io/docs/data-frontmatter/)
*   [版面前端数据](https://www.11ty.io/docs/layouts/#front-matter-data-in-layouts)
*   [模板数据文件](https://www.11ty.io/docs/data-template-dir/)
*   [目录数据文件(和升序父目录)](https://www.11ty.io/docs/data-template-dir/)
*   [全局数据文件](https://www.11ty.io/docs/data-global/)

### 部署

有许多选项可用于部署 11ty 站点。首先，我真的很喜欢在工作中使用网络生活。但是我认为任何人都可以选择其他部署方式，这也很好。

### 包装完毕

我们拥抱一项新技术的方法之一是尝试它。这是对 11ty 的一个快速介绍。相当多的开发者已经在用 eleventy 建立博客、网站、文档网站，我认为你应该试一试。