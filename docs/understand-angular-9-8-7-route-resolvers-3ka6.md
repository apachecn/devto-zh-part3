# 了解角度 9|8|7 路径解析器

> 原文：<https://dev.to/singhdigamber/understand-angular-9-8-7-route-resolvers-3ka6>

[懂角 7 路解析器](https://www.positronx.io/angular-7-route-resolvers/)的帖子最早出现在 [positronX.io](https://www.positronx.io) 上。

今天我们将了解第七角中的**路径解析器。为了显示从 API 获取的数据，我们必须将用户路由到一个组件。然后我们应该调用`ngOnInit` hook 服务中的一个方法来获取所需的数据。该组件能够显示数据的加载指示器。**

然而，Angular 7 提供了一个更好的方法，名为 route resolver。在路由解析器的帮助下，您将能够在组件准备好之前获取数据。

[点击此处阅读更多信息](https://www.positronx.io/angular-7-route-resolvers/)