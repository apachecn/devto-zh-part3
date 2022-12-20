# 用 Golang 实现简单的 JWT 认证(第 3 部分)

> 原文：<https://dev.to/omnisyle/simple-jwt-authentication-with-golang-part-3-1ja3>

这是由三部分组成的系列教程的第三部分，该教程为内部 API 构建了一个小而完整的 JWT 认证解决方案(大多数概念也可以应用于为公共 API 构建 JWT 认证)。

*   第 1 部分— [公钥/私钥生成和存储](https://dev.to/omnisyle/simple-jwt-authentication-for-golang-part-1-3kfo)
*   第 2 部分— [构建 CLI 以创建/检索应用程序对象](https://dev.to/omnisyle/simple-jwt-authentication-with-golang-part-2-263)
*   第 3 部分—构建 JWT 认证中间件

<figure>[![](img/83e654e8ce87e498d7080aea3371ad9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RG2pxQHC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQoUJBQLOv6Vh_pgEYctC_A.png)

<figcaption>[https://jwt . io](https://jwt.io)</figcaption>

</figure>

### 概述

这是本系列教程的最后一部分，我们将最终编写在授权头中使用 JWT 令牌验证请求的方法。这包括两个部分。

1.  JWT 认证功能
2.  使用 JWT 认证的中间件示例

### JWT 认证

在这一部分中，我们将使用一个 JWT 库为我们提供一种解析和编码 JWT 令牌的方法。它们有几个，你可以在 [jwt.io](https://jwt.io/) 选择你最喜欢的一个。在这个项目中，我选择了 [jwt-go](https://github.com/dgrijalva/jwt-go) 。我以前用过它，所以我比别人更熟悉它。

我觉得把 jwt-go 包装在自己的类里，只暴露我需要用的就好了。它有三个主要好处。

1.  我不需要记住这个库的文档，直到我需要它的另一个东西，因为我需要的所有东西都是我自己写的，并且有文档记录。
2.  这是学习如何使用这个库并真正理解它的界面的一个很好的方法。
3.  代码的其他部分不需要知道这个库，所以我们可以相对容易地切换到另一个库。

### 解析 JWT 令牌