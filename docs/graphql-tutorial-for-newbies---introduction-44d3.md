# GraphQL 新手教程-简介

> 原文：<https://dev.to/robmatyszewski/graphql-tutorial-for-newbies---introduction-44d3>

我是来自 GraphQL 编辑器的罗伯特。几天后，我会为新手发布一个 GraphQL 教程。这篇文章是对 GraphQL 的介绍。跟随我获取更多信息！

[![](img/9d26466e388c1d034a55fb4efc0cc22a.png)](https://graphqleditor.com)

## graph QL 从何而来？

GraphQL 于 2015 年由脸书推出，作为 REST 的替代产品。当时它不仅仅是一个实验——自 2012 年以来，它已经在 facebook 移动应用程序中投入生产。在公开发布后，它变得非常受欢迎，在那段时间，许多公司都在研究类似的解决方案。这项技术开始获得关注，因为它可以用于许多其他语言，并且有许多现成的库可以用于最流行的风格。根据 Stackshare 的数据，目前有近 2000 家公司在使用它。早期采用者包括 Coursera、Github、ProductHunt、Yelp 或 Twitter。

## 什么是 GraphQL？

为了介绍 GraphQL，我将使用一个数据示例，并将功能与 REST 进行比较。这个例子将向你展示为什么新技术更有效，更容易使用，以及创造它的动机是什么。

假设我们正在为两个版本(web 和移动)的应用程序创建一个后端。该系统的核心将包含关于足球运动员的信息，他们参加的球队和其他一些细节。应用程序的移动版本应该显示较少的信息。

| 网 | 移动的 |
| --- | --- |
| 姓名
简历
团队(描述，年)
团队(描述，年) | 名称
队(年)
队(年)
- |

因此，如果我们要基于 REST 创建 API，我们需要编写两个请求:

```
GET /player/{id}
{
  "id": „101”
  "name": „David Beckham”
  "bio": „…”
  „age”: „43”
}

GET /player/{id}/teams
{
  „Teams”: [{
    „id": „201”,
    „name”: „Real Madrid”,
    „description": „…”,
    „goals”: „…”,
    „matches”: „…”,
    „years”: „2003-2007”
  },
  ...
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

通过查看响应，您可以看到，在这两种情况下，我们都获得了比所需更多的数据。由于移动数据和加载速度，它在移动应用程序中是必不可少的。当然，我们可以通过编写不同的端点来克服这一点，这样我们只能得到我们想要的。另一种方法是以一种我们可以选择我们想要的方式实现 API，即 GET /player/{id}/teams？only =名称。但是在需求每周都在变化的敏捷软件开发中，这种解决方案很难维持。

## 与此相反，我们可以引入 GraphQL。

使用 GraphQL 的后端更加灵活，因为它只有一个端点。客户编写一个查询并指定您到底想要什么，然后在 JSON 中获取数据。在我们的足球示例中，我们可以为每个平台(web 和移动)创建查询。

Web 应用程序:

```
query {
  player(id: "101") {
    name
    bio
    teams {
      title
      description
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

响应:

```
{
  "data": {
    "player": {
      "name": "David Beckham",
      "bio": "...",
      "teams": [{
        "title": "Real Madrid",
        "description": "..."
      }, ...]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

移动应用程序

```
query {
  player(id: "101") {
    name
    teams {
      title
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

响应:

```
{
  "data": {
    "player": {
      "name": "David Beckham",
      "teams": [{
        "title": "Real Madrid"
      }, ...]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在每个查询中看到的，我们只得到我们想要的。这就是 GraphQL 的强大之处——灵活性。您不需要为不同的屏幕创建端点。各种客户端可以按照自己需要的方式使用曾经定义过的模式。

## 请求少。

在传统的 REST 方法中，通常会有很多 GET 请求在一个屏幕上显示数据。当涉及到移动应用程序时，它不是超级高效的——它降低了它们的速度，并且需要更多的互联网数据传输。GraphQL 只允许用户创建一个端点。由于脸书工程师引进的技术，客户端和服务器之间的通信得到了更好的优化。

[![](img/867611d308d3275679ac6a24a7cd2e23.png)](https://graphqleditor.com)

## 查询语言

GraphQL 最重要的特性是它是语言不可知的。它不是为了与特定技术一起工作而创建的。它是一种查询语言，有自己的规则，适用于大多数流行的编程语言。互联网上有许多支持 C#/的库。NET，PHP。Javascript、Java、Python、Ruby 等等。

## 总结

GraphQL 是一种查询语言和运行时，我们可以使用它来构建和公开 API 作为强类型模式，而不是数百个 REST 端点。您的客户会看到该模式。他们为他们想要的东西写一个查询。他们把它发送过去，并得到他们所要求的数据，仅此而已。GrapqhQL 优势:

*   更少的数据和对移动应用的请求，
*   简单和可见的 API 结构
*   可以与许多技术一起使用
*   被 Twitter、脸书、Github 和许多其他网站使用

如果你正在寻找最好的 graphql 教程，请查看这个[帖子](https://blog.graphqleditor.com/top-graphql-tutorials-reviewed-2019)。