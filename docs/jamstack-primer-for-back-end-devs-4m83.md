# 用于后端开发的 JAMstack 初级读本

> 原文：<https://dev.to/_codingblocks/jamstack-primer-for-back-end-devs-4m83>

JAMstack 是一种基于客户端 JavaScript、可重用 API 和预建标记构建 web 应用的方法。我认为对构建时渲染的强调对所有 web 开发人员都有有趣的意义，我在为一组后端开发人员做演示时制作了一个小视频。

<figure>

[https://www.youtube.com/embed/Z2JK7SS82wE](https://www.youtube.com/embed/Z2JK7SS82wE)

<figcaption>I tried doing it in a fun “pop-up video” style, and I’d love to know what you think about it!</figcaption>

</figure>

## 什么是 JAMstack？

简而言之，“JAMstack”是一组以构建时呈现和[内容交付网络](https://en.wikipedia.org/wiki/Content_delivery_network)为中心的最佳实践的便利名称。前 3 个字母是首字母缩略词:JAM = >、JavaScript、API 和标记。

“堆栈”更难确定。它的意思并不像 [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) 或[的意思](http://mean.io/)那样是“堆栈”,而是指一组[最佳实践](https://jamstack.org/best-practices/),它们一起工作提供的收益大于各部分的总和。

“JAMstack”这个名称有点争议，我不知道我们将来是否还会使用这个术语，但我确实认为它背后的思想和最佳实践会一直存在。

以下是与视频相对应的注释列表。

**JavaScript**

*   客户端 JS(重要！)
*   与框架无关， [Vanilla JS](http://vanilla-js.com/) 也很好！
*   没有服务器端渲染，我们在这里处理的唯一服务器是构建服务器

**API**

*   所有服务器端流程或持久性都被抽象成 API
*   所有的 API 都可以通过 HTTPS 从 JS 访问
*   将您自己的服务器端组件也视为第三方！

**Markup**

*   模板标记应该在部署时预先构建
*   现代站点生成器在这方面很棒
*   主要思想是把你的内容当作数据，与你的设计分开

**最佳实践(JAMstack 中的“堆栈”)**

*   应用程序应该分布在 CDN 上，越靠近边缘，UX 越好
*   现代构建工具:Bable、WebPack、Gatsby 等
*   运行应用程序所需的一切都在一个 git 存储库中——简单的 npm 安装
    *   还记得 FTP 吗？观察者现在更常见了
*   关注自动化构建，不太强调持久性，所以通常需要部署变更
*   原子部署(一次部署所有内容，而不是逐个文件地部署)
*   即时缓存失效(使用可以处理即时缓存清除的 CDN)
*   [阅读更多关于最佳实践的信息](https://jamstack.org/best-practices/)

* * *

## 好处

*   通过静态文件/CDN 提高性能
*   更便宜的扩展(CDN)
*   安全性(服务器与前端分离)
*   更好的开发者体验
    *   松耦合
    *   目标编码和调试

* * *

## 什么不是 JAMstack

*   当您的视图不完全是客户端时(意味着前端和后端紧密耦合)
*   在运行时构建视图(ASP Razor，Ruby，Node 可以用于 API，但不能用于视图)
*   像 WordPress 这样的东西需要 PHP 这样的应用服务器来向用户呈现 html

* * *

## 不使用 JAMstack 时

除非您实际上能够在构建时呈现一些数据，否则您无法获得构建时呈现的好处。如果你的站点有很多实时数据或者数据是特定于用户的，那么对你来说没有太大的好处——你的 JAMstack 站点基本上只是一个 SPA。

* * *

## 资源

[JAMstack.org](https://jamstack.org/)

[JAMstack 电台播客](https://www.heavybit.com/library/podcasts/jamstack-radio/)

[示例网站](https://jamstack.org/examples/)

[更多视频](https://jamstack.org/resources/)