# FeathersJS 频道订阅

> 原文：<https://dev.to/mattchewone/feathersjs-channel-subscriptions-4ma3>

<figure>[![](img/a43387af19533e90ed1c46ddb836ac29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GeMK1j9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKJBI9GVIZQQQb3BDqRdmxA.jpeg) 

<figcaption>照片由 [chuttersnap](https://unsplash.com/photos/qMMzHPF3ajQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/channels?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

FeathersJS 通道是处理实时数据的好方法。但是，有时您可能只需要某个页面上的某些数据。例如，您可能有一个聊天室应用程序，它有多个房间，而您只想提供用户正在查看的房间的数据。

### [服务器上的](#on-the-server)

为了能够动态地处理通道，我将配置一个自定义服务来处理通道的加入和离开。