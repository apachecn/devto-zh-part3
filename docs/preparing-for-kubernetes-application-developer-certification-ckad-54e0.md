# 准备 Kubernetes 应用程序开发人员认证(CKAD)

> 原文：<https://dev.to/matthias/preparing-for-kubernetes-application-developer-certification-ckad-54e0>

几周前，我决定获得认证。

在我的工作中，我专注于 web 开发(后端和前端)，主要是 Node.js 和 TypeScript，但我也负责在我们托管的 Kubernetes 集群中部署应用程序。当其他团队遇到 Docker 或 Kubernetes 相关问题时，我也会提供支持。还有一些遗留应用程序运行在 Apache Tomcat servlet 容器中，或者通过 Apache2 服务器提供。

当我在寻找一个合适的认证时，我发现了 [Kubernetes 应用程序开发人员认证(CKAD)](https://www.cncf.io/certification/ckad/) 。

> 认证 Kubernetes 应用程序开发人员(CKAD)认证的目的是保证 CKAD 具备履行 Kubernetes 应用程序开发人员职责的技能、知识和能力。经过认证的 Kubernetes 应用程序开发人员可以定义应用程序资源，并使用核心原语在 Kubernetes 中构建、监控和排除可伸缩应用程序和工具的故障。

该证书的范围似乎非常适合我所寻找的。

我问我的雇主他们是否会支付考试费用(考试费用只有 300 美元)。当我的计划得到积极的反馈时，我开始为考试学习。

在这里我想分享一下我的发现。

你有两个小时完成 19 道题。没有多项选择题，只有在专门为您创建的群中动手操作。
这些问题分为七个不同的领域(每个领域都有不同的权重):

*   核心概念(13%)
*   配置(18%)
*   多容器容器(10%)
*   可观察性(18%)
*   豆荚设计(20%)
*   服务和网络(13%)
*   状态持久性(8%)

考试期间，除了以下网站，您不得浏览其他任何网站:

*   https://kubernetes.io/docs/及其子域
*   https://github.com/kubernetes/及其子域
*   [https://kubernetes.io/blog/](https://kubernetes.io/blog/)

还禁止使用除考试桌面、物理笔记或记事本、书籍、移动设备或手册之外的设备。所以我想在开始考试前清理你的桌面是个好主意。

当我第一次读到这些限制时，我问自己他们将如何检查它。在考试过程中，监考人会通过音频、视频和屏幕共享的方式对你进行监控。不可能作弊，你也不应该作弊，因为在监考人两次警告后，考试将被终止。

听起来有点可怕，对吗？无论如何，我还是想获得认证，所以这里有一些资源可以帮助我准备考试:

*   [官方 CKA 和 CKAD 考生手册(PDF)](https://training.linuxfoundation.org/wp-content/uploads/2019/05/CKA-CKAD-Candidate-Handbook-v1.2-.pdf)
*   [CKAD 网站](https://www.cncf.io/certification/ckad/)
*   [CNCF 认证课程的开源课程](https://github.com/cncf/curriculum)
*   [立方文献](https://kubernetes.io/docs)

*   【Kubernetes 认证应用开发者(CKAD)简而言之

*   [云原生计算基金会的一套 Kubernetes 应用程序开发人员认证考试准备练习](https://github.com/dgkanatsios/CKAD-exercises)

*   [通过认证 Kubernetes 应用程序开发员(CKAD)考试的资源和注意事项列表](https://github.com/twajr/ckad-prep-notes) 

*   【Kubernetes 应用程序开发员(CKAD)认证模拟考试

*   [准备 Kubernetes 认证应用程序开发员(CKAD)考试，以及它与 CKA](https://blogs.msdn.microsoft.com/arsen/2018/07/05/preparing-for-certified-kubernetes-application-developer-ckad-exam-and-how-it-compares-to-cka/) 的比较

*   [CKAD 考试失败](https://medium.com/@MrMcMuffins/ckad-exam-failure-748f978a325a)评论

*   [学习指南:认证 Kubernetes 应用开发者(CKAD)考试](https://www.cloudreach.com/blog/study-guide-certified-kubernetes-application-developer-ckad-exam/)

*   [关于 Kubernetes CKA 和 CKAD 认证的思考](https://talkcloudlytome.com/thoughts-on-the-kubernetes-cka-and-ckad-certifications/)

还有一些视频值得一看:

*   [准备 Kubernetes 认证应用开发人员(CKAD)的提示](https://youtu.be/rnemKrveZks)
*   [关于 CKA 和 CKAD 你需要知道的一切](https://youtu.be/WFGPArjYMbQ)
*   [立方认证课程- CKAD](https://youtu.be/oIP13PZnPAk)

然而，我认为最重要的事情是使用真正的 Kubernetes 集群。

我真的很兴奋，不管我是否能通过考试。幸运的是，你有一次免费重考机会。

你还有什么其他的建议或忠告给我吗？请与我分享你的 CKAD 或 CKA 经验。

* * *

如果你喜欢我的内容，你可能想在 Twitter 上关注我？！ [@fullstack_to](https://twitter.com/fullstack_to)