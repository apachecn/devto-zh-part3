# 我想创建基于 Docker 的平台即服务。你是怎么想的？

> 原文：<https://dev.to/acro5piano/i-would-like-to-create-docker-based-paas-how-do-you-think-fdb>

# 这是什么

据说“先卖后建”，所以我想听听你对我的原型的看法。

这只是一个演示图像，但你可以找到重点是什么。

[![image](img/d65b382d74de1e1802397a90124004ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtTOSDxH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ts8dhg4dobhoekyrr3u.gif)

# docker-run.com

[https://docker-run.com](https://docker-run.com)

docker-run.com 使您能够在生产中运行 Docker 容器。你所要做的就是填写你的 Docker 图片的 url。

# 为什么

我们都知道 Docker 在生产环境中是很棒的。无状态部署、开发和生产中的共享环境等等。然而，当我们在生产中管理我们的容器时，我们必须做很多工作，例如蓝绿部署、容器健康检查和学习 Kubernates。你能想象我们仅仅通过告诉你的 Docker 图片的 url 就能运行 Docker 容器吗？

*   不需要创建 Fargate 实例
*   不需要学习 Kubernates
*   不需要编写部署脚本
*   不需要关心可扩展性
*   无需设置您的证书

是的，docker-run.com 在这里:一个新的和更好的方式来运行码头集装箱的生产。

可能的替代方案:

*   [https://zeit.co/now](https://zeit.co/now)
*   [https://quay.io](https://quay.io)

# 如何使用

正如你在上面的演示中看到的，你所要做的就是输入你的 docker 图片 url。docker-run.com 为你的应用程序创建一个唯一的域。

部署？这很容易，不需要安装任何工具:

```
curl -XPOST -H 'Authorization: YOUR_TOKEN' -d HOST/IMAGE:VERSION https://docker-run.com/APP_ID/renew 
```

Enter fullscreen mode Exit fullscreen mode

码头集装箱将在不停机的情况下更新。

# 进一步信息

[https://github . com/acro 5 piano/docker-run . com](https://github.com/acro5piano/docker-run.com)

目前 docker-run.com 是阿尔法。我们将于 2019 年 4 月推出测试版，敬请关注。