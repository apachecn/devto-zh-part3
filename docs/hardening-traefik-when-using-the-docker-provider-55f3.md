# 使用 Docker 提供程序时强化 Traefik

> 原文：<https://dev.to/tomwerneruk/hardening-traefik-when-using-the-docker-provider-55f3>

前几天 Traefik GitHub tracker 上的这个[问题](https://github.com/containous/traefik/issues/4174)激起了我的兴趣。Docker 套接字有时确实是致命的弱点，有不同的缓解措施来保护它——代理容器，通过 TLS 暴露身份验证和授权。你的选择取决于你的风险偏好和你所处的大环境。

在这种情况下，将 Docker 套接字绑定到面向互联网的容器上不是我想冒的风险。一些选项已经浮出水面(Sock 代理，切换到 TLS 等。切换到静态文件配置)。然而，我想尝试另一条路线。

结果是`traefik-prism`。这是一个简单的 Python 脚本，它接受一个有效的 Traefik 动态配置，并将其发布到一个面向互联网的容器中，该容器不附属于 Docker。

<figure>[![Hardening Traefik when using the Docker Provider](img/8c74e49f1279f5bdc4de289653770ca1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j_bCVXUF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://fluffycloudsandlines.blog/conteimg/2018/12/traefik-prism-architecture.png) 

<figcaption>**示例部署为 traefik-prism**</figcaption>

</figure>

它利用了 Traefik 中内置的 API，这里没有魔法。它旨在处理不同的 Traefik 提供者，而不仅仅是 Docker(提示:热烈欢迎来自其他提供者用户的评论)。大致，剧本；

*   从 API 端点(通常是端口 8080)上的`/api`中检索当前动态配置(前端和后端)，
*   基于`PROVIDERS`环境变量，从响应中提取前端和后端，然后将潜在的多个块(比如`file`和`docker`)合并到一个配置中，
*   最后，将新的合并配置推送到`/api/endpoints/rest`。新配置应该立即激活。

从较高安全性级别区域(在本例中是可以访问 Docker 套接字的容器)推送至较低安全级别区域的安全模型是保护系统的常见模式。这里没有任何“敏感”数据泄露，所以当我们从高到低移动时，我不太担心内容检查。

查看 [GitHub](https://github.com/tomwerneruk/traefik-prism) 上的代码，或者直接从 [Docker Hub](https://hub.docker.com/r/tomwerneruk/traefik-prism/) 中提取图像。

一如既往，随时欢迎您的想法和评论！