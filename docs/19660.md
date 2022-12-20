# Angular:在同一 URL 导航上重新提取数据

> 原文：<https://dev.to/kreuzerk/angular-refetch-data-on-same-url-navigation-1nle>

#### 不同的方法各有利弊

[![](img/a2671840deb46f770697d8cde4d70799.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---ZFI7hIW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AHdF1NiaTQUXwN4V1zp57Ag.jpeg)

实时应用程序通常使用 WebSockets 或一些服务器推送技术。如果后端不支持推送技术，我们通常会使用某种轮询机制来获取最新数据。

然而，在一些用例中，我们希望将控制权交给用户，让他决定何时刷新数据。例如通过在特定路线已经显示时点击刷新按钮或路线导航项目。

默认情况下，当我们试图导航到已经激活的同一个 URL 时，路由器不会发出路由器事件。这通常是件好事。

但是我们如何在路由刷新时重新提取数据呢？🤔

[了解更多信息...](https://medium.com/@kevinkreuzer/refresh-current-route-in-angular-512a19d58f6e)