# 使用 Swashbuckle 生成 Swagger 示例动态请求有效负载

> 原文：<https://dev.to/joni2nja/generating-swagger-example-dynamic-request-payload-with-swashbuckle-4lej>

这篇文章最初出现在[媒体](https://medium.com/@joni2nja/generating-swagger-example-dynamic-request-payload-with-swashbuckle-77c09127392c)上

* * *

*新到 Swashbuckle？见本官* [*《霸王花和 ASP.NET 核心入门》*](https://docs.microsoft.com/en-us/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-2.2&tabs=visual-studio)

我们可以用 Swashbuckle 生成 Swagger 示例请求，见[本帖](https://mattfrear.com/2016/01/25/generating-swagger-example-requests-with-swashbuckle/)。

假设我们有以下端点:

[![](img/c4a057b0ec8c755a1451f47b5269df8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_37yggZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AW7ZxtmoM5Kz4EdzlcK8BYg.png)

忽略这个奇怪的实现，它只是一个示例。关注方法签名。

这是 Swagger UI 页面:

[![](img/b5bcb901c2bbe5aad12c5d27ec46a62a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gj1gC8tF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AugI0zzrOp2_vJprrHa-IXQ.png)

*注意，这个帖子使用的是* [*霸气。AspNetCore 版*](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/tree/v3.0.0) *。*

现在假设我们别无选择，只能让它接受动态有效负载——也许是由于客户的需求变化。所以我们把`PostThorRequest request`改成`Jobject request`，就像这样:

[![](img/e3eea4e533e0e7bd989b0d87e94c384b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zw1XCUX_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXWlexTROAKpC6tHKlTJLJw.png)

现在，当我们运行它时…

[![](img/18176c76522e0e00dc6b070b13fd6b29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--80vLmTV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASaUP72Y12b9WItTqs8_dhg.png)

啊哦…看到那个，榜样价值没了！

如何解决这个问题？

将 SwaggerRequestExample 类型的第一个参数更改为 job object 似乎可以解决这个问题。

[![](img/f77f707b36b2e840d206c6b9fd60e5b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KhAmy_z1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoKeMWoRFqY5AWv7kffX7tg.png)

还有…

[![](img/e5a3591e83f3650c81ee7dd43ae6efc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29slQnBT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ae_4-GT5LFeMNIrDkTg8P9w.png)

瞧啊。它回来了！

请注意，这可能只是可行解决方案的一种方式。虽然它确实有效，但就我个人而言，我不推荐这种面向公众的 API 的动态请求方法。