# Kubernetes 黑客马拉松的客户体验

> 原文：<https://dev.to/azure/experience-of-kubernetes-hackathon-with-customers-4oem>

### TL；速度三角形定位法(dead reckoning)

## 历史:

首先，我想解释一下为什么我从去年开始就多次打开 k8s 黑客马拉松。

事实上，k8s 不仅面向基础设施工程师，也面向开发者工程师。为了恰当地使用 k8s，这两种技术知识都是需要的。对我来说，我花了几个月去理解所有的 k8。然而，这对每个人来说都太长了，他们想更顺利地赶上它。

在过去，作为一个曾经的传道者和现在的开发者拥护者，我当然做了很多关于 k8s 的演讲。然而，我认为我很难在一次演示中仅用一个小时来解释 k8s 的重要主题。

因为有时候我需要解释 12 因子 App，CI/CD，微服务要看受众水平。

在过去，我创造了许多实验室内容的技术手。是的，HoL 对初次接触非常有用。然而，如果用户遇到麻烦，他们不能从 HoL 内容中解决问题。因此，我认为与会者应该在 HoL 或 Hackathon 期间考虑更多。

从去年开始，我在日本(东京，札幌，实际客户)和澳大利亚开始了 k8s 黑客马拉松。在黑客马拉松期间，我首先会收集参与者的要求。根据要求，我将帮助并建议与会者达到要求。

## 关于黑客马拉松事件

一般来说，每次与会者的技术水平是如此的不同。有时成员没有 Azure 的经验，即使他们有 AWS 的经验。有些工程师不是开发者。一些工程师不熟悉基础设施。

在这种情况下，我会首先告知他们容器、微服务、k8s 和 Azure 的基本概念。

对于社区事件，很难创建制作就绪的内容。因为几乎所有的社区活动都计划只有 2 天。因此，几乎所有的与会者可能只是在当天赶上了如何使用、故障排除和考虑架构。

对于客户事件，当然取决于客户和他们的要求，他们将能够做更多的事情。

例如，如果他们可以准备为期一周的黑客马拉松，并且他们希望将他们的应用程序迁移到 k8s，那么这一周就有可能完成。

[![](img/254bd88b7f61d303656dddc72f14bff6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kjj8We7r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jcmnt402cxalt752usu8.jpg)

### 日期:

*   社区活动:需要 2 天(周六、周日上午 10:00-18:00)
*   客户活动:需要 3-5 天(周一至周五:上午 10:00-18:00)

### 活动

#### 1。基本概念的演示(介绍)(1.5 小时)

*   改变 IT 行业的世界
*   利用价值流图改进开发工作流程
*   云原生应用的重要性
*   12 因素应用程序
*   微服务
*   为什么集装箱和 k8s 是趋势和重要的
*   k8s 的适用和不适用的应用/系统/架构
*   集装箱基础(码头工人)

首先，我会解释为什么现在容器和 k8s 很重要。以及容器或 k8s 在什么样的情况下有用。我还告诉他们什么样的情况下你不应该使用集装箱和 k8s。
因为集装箱和 k8s 不是银弹。如果用户在错误的空间使用它们，他们可能会困扰他们的管理。

[![](img/58dd08d34ad89906d326749ec34dfe74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Cdc0-fj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uua95dy0ps1tc970un6k.jpg)

### 2。黑客马拉松(1.5 天)

这不是 HoL，而是黑客马拉松。所以我会在这两天收集他们想做或解决什么样的工作的信息。在那之后，我创建了这个组，并按照 Mob 编程风格进行。

[![](img/36da549c9299f24cb0f2a2b4c54d1a5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BZTPzv0n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cqsoefhq54pve1z9kpru.jpg)

基本操作:

1.  在 Azure 上创建 Linux 虚拟机
2.  在 Linux 虚拟机上安装 docker 客户端
3.  创建示例应用程序(Java/PHP/Python/)。网络核心)
4.  安装 Azure 容器注册表
5.  创建 Docker 映像并将其推送到注册表
6.  安装 az 命令
7.  在 Azure 上安装 AKS
8.  创建部署 YAML
9.  创建服务 YAML
10.  创建入口 YAML
11.  通过就绪性/活性探测评估自我修复
12.  评估滚动升级
13.  创建虚拟节点
14.  通过 Azure DevOps 创建 CI/CD 环境
15.  创建监控
16.  Istio
17.  ....取决于与会者...

### 地点:

对了，场地和设施对开黑客马拉松很重要。每次，我都用 Mob 编程风格来开启黑客马拉松。为了做到这一点，每个团队都需要大屏幕(显示器)。我们还需要高带宽网络连接到 Azure。

幸运的是，微软办公室的每个会议室都有大屏幕显示器，所以很容易打开 Hacakathon。然而在办公室之外，有时我们需要照顾它。

## Hackahon 的结果:

根据与会者的反馈，几乎 80%的与会者对 k8s 黑客马拉松表示满意。事实上，即使他们是初学者，他们也只能学习和操作 k8s 两天。尽管他们不熟悉 Azure，但他们认为 Azure 的优点包括 AKS、Azure DevOps、Virtual Node 和 Azure Dev Spaces。

参考以下博客。

## 与会者的反馈&博文

***注:***

以下内容是我直接参与并由与会者用日语写的，如果您参考以下博客条目，请使用类似 [Bing Translator](https://www.bing.com/translator) 或 [Google Translator](https://translate.google.com/) ？

* * *

2018/05/15 [大石九 ra Daichi](https://www.oisixradaichi.co.jp/en/)

*   [微软官方客户故事](https://customers.microsoft.com/ja-jp/story/oisix-manufacturing-azure-api-monitor-mysql-jp-japan)
*   [新闻稿:Oisix ra Daichi](https://www.oisixradaichi.co.jp/wp/wp-content/uploads/2019/03/11d7d37d7306a49881a19f4062fa5876.pdf)
*   [用 Azure 搭建的微软 Hackfest 微服务平台](http://creators.oisix.co.jp/entry/2018/05/15/180000)
*   [网媒:将 Oisix 运营了 19 年的单片服务推广为微服务，Kubernetes 为什么能投入生产运营](https://www.itmedia.co.jp/enterprise/articles/1903/18/news016.html)
*   [演讲:Oisix 的微服务提供安全可靠的食品](https://www.slideshare.net/hiroakikobayashi1806/oisix)

2018/7/7 日- 7/8 : [日本 Java 用户组 2 日 k8s Hack](http://www.java-users.jp/?p=2979)

*   [团队 1 日志 5j:演示文稿](https://docs.google.com/presentation/d/1lQJIj-Ge8os6bUUiGbpF4MiaWkXd4Aqo6u1I5vTTafY/edit#slide=id.gc6f80d1ff_0_50)
*   [团队 2 CI/CD 与 GitLab:演示](https://docs.google.com/presentation/d/1nlJXMBl7g8m9qZYl7NzLqiHA2TnZLyKh5v4ND_gA73U/edit#slide=id.p)
*   [团队 3 Istio(像帆船一样):演示](https://docs.google.com/presentation/d/10dLs40lqd9djQwPj6yDaYzIIlBhPXK9u4lG7aS5k1WU/edit#slide=id.p)

*   [博客:用黑客马拉松体验 Mob 编程](https://qiita.com/solaris1000/items/37742ec2731cf8730ded)

2018/8/7-8/10: [Pnop:合作公司(和 MVP)](https://www.pnop.co.jp/)

*   [Azure Kubernetes 服务(AKS)提示 1](https://blog.azure.moe/2018/08/12/azure-kubernetes-service-aks-tips-1/)
*   [Azure Kubernetes 服务(AKS)提示 2](https://blog.azure.moe/2018/08/15/azure-kubernetes-service-aks-tips-2-rbac/)

2018/11/17(土)~18(日)[札幌 JavaDo(札幌 Java 用户组)](https://javado.connpass.com/event/107627/)

*   [我在 Java Do 社区尝试了 Kubernetes 的 Hackahon](https://bitstar.jp/blog/2018/12/19/23659/)

2019/01/21-1/25 [客户:心跳](https://heartbeats.jp/)

*   [构建 Azure Kubernetes 服务(AKS)并使用 Azure DevOps 创建 CI / CD 管道](http://kaz29.hatenablog.com/entry/2019/01/28/101016)
*   [我参加了微软 Kubernetes (AKS) DevOps 研讨会](https://heartbeats.jp/hbblog/2019/02/ms-aks-azure-devops-workshop.html)
*   [发布 azmon，Azure Monitor 的监控插件](https://heartbeats.jp/hbblog/2019/04/azmon.html)

2019/02/23-/2/24:[Kubernetes Hackathon 在 KanJava(日本地区西侧的 Java 社区)](https://kanjava.connpass.com/event/118967/)

[“Azure”对于 Kubernetes 来说是什么？我去了 hackathon，所以我们过去复习一下](https://qiita.com/manta35/items/2b7059e506ce83df8a11)

2019/03/19:[OiStudy # 1 Azure&AKS:社区活动](https://oi-study.connpass.com/event/122427/)

2019/04/13-4/14[kita azu:JAZUG Sapporo 第 22 次研究](https://jazug.connpass.com/event/123302/)

*   [札幌第 22 大街(北蓝色)~由高雪富瓦](https://nagumo.co/azure/857)提供的蓝方库柏服务(AK)】
*   [第 22 届札幌 JAZUG(kita azu)～Azure Kubernetes Service(AKS)松田隼安明](https://vnext-y-blog.azurewebsites.net/archives/6278)
*   我已经研究 Kubernetes 两天了#JAZUG
*   [我参加了“JAZUG 札幌分公司(Kitaazu)第 22 次学习会议 Azure Kubernetes Service(AKS)Hackathon”](https://spyke.hatenablog.com/entry/2019/04/15/234932)

2019/04/2-4/4 [客户 Mercari:销售 App](https://www.mercari.com/)

*   [使用 Azure Kubernetes 服务(AKS)对 Mercari Web 进行自动化 UI 测试(Selenium Grid/Zalenium)](https://tech.mercari.com/entry/2019/04/16/060000)

很庆幸微软之外有大量 AKS 相关的技术内容。AKS 的使用和日本社区正在成长！！

### 其他:

我从黑客马拉松中使用的 Slack Channel 获得了许多关于产品和活动的直接反馈。

[![](img/6e8b1e3ebab29535d50128409b925d6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HV0W0L-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d20kjqbif2plnfabd9qh.jpg)
[![](img/17c1294af28de74d4b64caff70cbe7f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wXwZafLQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmt7j75u8rlzyf0vf32i.jpg)
[![](img/3f4877cf565f744905cd48da815be23c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--txRxWzFs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l6cvp5mvngizxr26dj5l.jpg)

## 终于

如果你愿意和我一起参加黑客马拉松，如果你能接受以下规则之一，取决于我的时间表，我可以和你合作。即使它不在日本，对我来说也没问题。

*   你的公司可以像 Oisix 一样打开 Microsof 客户故事
*   您正在考虑将 AKS 用于生产，如 Oisix/HeartBeats
*   在黑客马拉松期间向我们提供产品反馈
*   一些工程师可以把你在社区活动中的经历写在博客上
*   对于社区:你需要激活 Azure