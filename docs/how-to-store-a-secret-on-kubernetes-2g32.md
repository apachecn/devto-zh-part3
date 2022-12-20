# 如何在 Kubernetes 上存储一个秘密？

> 原文：<https://dev.to/omerlh/how-to-store-a-secret-on-kubernetes-2g32>

在 [Soluto](https://www.solutotlv.com/) ，我们在生产中使用 Kubernetes 平台已经一年多了。我们面临的挑战之一是机密管理，这根本不是一项简单的任务(您遇到过类似的问题吗？).Kubernetes Secret 对象有其自身的局限性(特别是，如何存储清单文件)。我们也尝试了各种解决方案(比如[密封秘密](https://github.com/bitnami-labs/sealed-secrets)和[头盔秘密](https://github.com/futuresimple/helm-secrets)等等)，但是没有一个适合我们。我们想要的解决方案是:

*   GitOps(因此它可以支持我们的分散运营文化)
*   零信任(一旦秘密被加密，就没有必要也没有办法解密)

这就是我们建造 [Kamus](https://github.com/Soluto/kamus) 的原因——我们的秘密加密解决方案。Kamus 让你无缝加密秘密，只有在生产中运行的应用程序才能解密。
我非常乐意回答你关于 Kamus 的任何问题，当然，期待听到你的反馈！