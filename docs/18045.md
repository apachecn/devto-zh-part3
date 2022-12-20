# 使用 React 路由器在 Netlify 上找不到页面

> 原文：<https://dev.to/dance2die/page-not-found-on-netlify-with-react-router-58mc>

*Photo by [你好我是尼克](https://unsplash.com/photos/MAgPyHRO0AA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/search/photos/direction?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*

* * *

如果你已经在使用`netlify.toml`，看看这篇文章，

[![nkemjiks image](img/7618319f2edca7472521392dae9be7c8.png)](/nkemjiks) [## 部署到 Netlify 时，解决 React 应用程序中的路由问题

### 姆博努祝福 2 月 26 日 201 分钟阅读

#netlify #react #deploy](/nkemjiks/resolve-routing-problem-in-your-react-app-when-you-deploy-to-netlify-119p)

* * *

使用 React Router to Netlify 部署单页面 React 站点后，您可能会遇到以下错误。

> **找不到页面**
> 
> 您似乎点击了一个断开的链接或输入了一个在此网站上不存在的 URL。

<figure>[![](img/e971a1342c255fb570e52c9299b63868.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mc8_iRD0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/12/page-not-found.jpg%3Fw%3D1170%26ssl%3D1) 

<figcaption>网络故障信息</figcaption>

</figure>

## 错误是怎么发生的？

React 路由器在客户端(浏览器)处理路由，所以当你访问非根页面(如`https://yoursite.netlify.com/else`)时，Netlify(服务器端)不知道如何处理路由。

*(因为你的路线是在根级别设置的)。*

## 错误代码&演示

下面是使用 React 路由器的最简单的代码。