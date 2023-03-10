# 部署 Django 的途径

> 原文：<https://dev.to/steelwolf180/path-towards-deploying-django-1mn1>

[![Photo by Caleb Jones on Unsplash](img/f183f9be801ce349c89966e4fda17ca5.png)](//images.ctfassets.net/ly2f59p4unnn/7kP4LEVsa1cYyFA4G7S7Pc/21da9b270c92c226a41a46985e97b424/Webp.net-resizeimage.jpg)

# 简介

对于许多刚开始从事 Django 开发的人来说，部署 Django 经常是一个令人困惑和困难的话题。

在部署 Django 项目的过程中，尝试各种部署选项以真正获得正确的技术或实践需要时间。

我将介绍部署您的 Django web 应用程序的各种方法，我希望它对任何初学者都有用。

# 常见问题部署

从字面上看，这可以归结为三个问题，当您打算部署一个 Django 项目时，您应该问自己。

*   部署的成本是多少？
*   你需要它缩放吗？
*   你打算维持多久？

务必注意，为了方便起见，你是唯一的人。谁负责部署您的 Django 项目。

选择像 Heroku、T2、里诺德、T4、数字海洋这样的云提供商就行了，因为这样做有点矫枉过正。

# 部署的成本是多少？

通过使用像 [Heroku](https://www.heroku.com/) 、 [Linode](https://www.linode.com/) 、 [DigitalOcean](https://www.digitalocean.com/) 或 [Python Anywhere](https://www.pythonanywhere.com/) 这样的**云提供商**来支付便利。

请注意，如果您计划大幅降低部署**的成本**，您需要了解与简化流程相关的各种技术。

这确实包括部署过程的维护成本和监控服务器健康状况的 T2 成本。

以防止它崩溃，如果你的服务器已经超载，通过多个请求或 DDoS 攻击。

# 您需要定标吗？

当您计划部署 Django 应用程序时，您需要估计您的体系结构的每月用户数量。

针对数万或数十万月用户的扩展与针对一百万月用户的扩展之间存在巨大差异。

所需的规模和架构变化的数量引人注目地导致了[开发者运营(DevOps)](https://aws.amazon.com/devops/what-is-devops/) 或[站点可靠性工程师(SRE)](https://landing.google.com/sre/interview/ben-treynor/) 的领域。

它使用了许多工具和实践，甚至借鉴了 [IT 服务管理](https://freshservice.com/itsm)的剧本来维护 Django 项目的可用性和性能。

我很确定这不是软件开发中经常教授的内容。

为了了解如何扩展 Django，我建议看一本名为[高性能 Django](https://highperformancedjango.com/) 的书。

这是作者 [PyDanny](https://www.amazon.com/gp/customer-reviews/R3TZ5O2XFB8OM/ref=cm_cr_dp_d_rvw_ttl?ie=UTF8&ASIN=1508748128) 向[强烈推荐的两勺姜戈](https://www.twoscoopspress.com/)。

当我开始在 Django 学习开发时，我把它作为参考指南的一部分。

# 你打算维持多久？

仅仅通过遵循 DevOps 实践或采用像 [Zappa](https://github.com/Miserlou/Zappa) 或 [Docker](https://www.docker.com/) 这样的技术来维护 Django 的部署基础设施并不容易。

可能需要几周或几个月的时间来完善和平稳运行。如果您对在生产环境中部署 Django 的体验感兴趣。

我在 Randall Degges 的文章[中遇到了**为 Django**](https://www.rdegges.com/2011/deploying-django/) 部署我现在所在的初创公司。

这是在我们采用 CI/CD 实践和使用 Docker 容器使部署和开发变得更加容易之前的事情了。

# 结论

我希望这篇文章对任何使用 Django 进行开发并期待部署的人有所帮助。

如果你是唯一一个部署 Django 项目的人，你可以看看使用云提供商或者使用 [Zappa](https://github.com/Miserlou/Zappa) 来利用 AWS Lambda 和 API 网关来使用 AWS 部署你的 Django 项目。

最后，在将您的 Django web 应用程序或 REST API 端点部署到您的生产环境之前，请查看 Django 中的[部署清单](https://docs.djangoproject.com/en/2.2/howto/deployment/checklist/)和[安全性，以防止安全漏洞。](https://docs.djangoproject.com/en/2.2/topics/security/)

如果你喜欢我的文章，请**注册**Max[冒险家简讯](http://eepurl.com/dOUoUb)获取我每周在 **Python** 、**创业**和 **Web 开发**偶然发现的牛逼内容。

你也可以**关注**我来获取我在 **Dev** 上的文章的**最新**更新

这篇文章最初发表在 Max 的博客上，地址是部署 Django 的途径——阅读时间:3 分钟和[照片由 Caleb Jones 在 Unsplash 上拍摄](https://unsplash.com/photos/J3JMyXWQHXU)

# 参考文献

*   [高性能 Django](https://highperformancedjango.com/)
*   两勺姜戈
*   扎帕
*   [部署 Django](https://www.rdegges.com/2011/deploying-django/)
*   第一部分
*   [部署清单](https://docs.djangoproject.com/en/2.2/howto/deployment/checklist/)
*   [Django 的安全](https://docs.djangoproject.com/en/2.2/topics/security/)