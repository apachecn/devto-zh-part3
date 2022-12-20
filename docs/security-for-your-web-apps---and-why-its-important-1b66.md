# 您的 Web 应用程序的安全性-以及为什么它很重要

> 原文：<https://dev.to/rjjsoftware/security-for-your-web-apps---and-why-its-important-1b66>

您公司的 web 应用程序是您客户的门户。

人们浏览亚马逊网站，挑选一件商品，直接给杰夫·贝索斯打电话，在电话里告诉他信用卡的详细信息，然后在 2-4 周内收到他们的产品，这样的日子已经一去不复返了。几乎所有的事情都在网上完成。

你的客户几乎没有人会去你的总部；很少有人(如果有的话)会给你公开的电话号码打电话；少数人会给你发电子邮件；一小部分人会使用在线聊天系统(如果你提供的话)；但是*你所有的*客户都会使用你的网络应用。

### 黑客比比皆是

我个人不喜欢使用短语`hackers`，但那是因为我更喜欢这个术语的旧含义:

> 创建代码时不使用工程过程的人。他们“在代码库中到处乱砍，盲目地删除代码，试图让它工作”

但是如果鞋子合适...

当然，现在很多黑客会对你的服务器发起复杂的攻击——这仍然是一个合理的担忧。但是有了 Azure、AWS 和 GCP 这样的服务，恶意的人就越来越难侵入你的基础设施。

###### 注

我前面的陈述假设基础设施是在考虑安全性最佳实践的情况下建立的。即无虚拟机(WebApps 或无服务器架构)、服务主体而非用户名和密码、多因素身份验证、应用最低特权原则

* * *

针对客户端(在本例中是浏览器)的攻击一直存在，但它们正慢慢变得越来越普遍。许多人通过利用谷歌模仿技术开始这个领域，然后他们继续使用 T2 脚本小子使用的技术。这些都是简单的技术，你可以直接复制和粘贴，而不必理解它们是做什么的。

事实上，这很容易做到，特洛伊·亨特让他(当时)3 岁的儿子在摄像机前表演了一次 SQL 注入攻击。

这些技术可以与在 Shodan 上快速搜索的结果进行对比，从而对易受攻击的系统发起攻击。

##### 亲提示:

让您组织内的某个人经常在 Shodan 上进行搜索，以确保您没有打开任何进入您的应用程序或基础架构的潜在门户。

如果你不这样做，有人会恶意。

* * *

### 安全为特征

哦，我们(在 RJJ)经常看到，安全性被作为最后一分钟的功能。这可能是，而且经常是，灾难性的。

我们已经看到了这样的应用程序，其中安全性是事后添加的，但是闯入系统是轻而易举的事情(参见我之前关于使用脚本小子技术是多么容易的评论)。

这导致安全团队成为开发团队的敌人。但是，正如开发人员和运营人员一样，我们都需要共同努力，确保我们构建的应用程序从一开始就是安全的。对于这种寓言式的证明，我推荐阅读[凤凰计划](https://www.goodreads.com/book/show/17255186-the-phoenix-project)。

最安全的应用程序——就像最安全的建筑一样——从一开始就将安全性融入了设计中。这允许我们将安全检查作为自动化测试套件的一部分。

你正在使用自动化测试，对吗？

这确保了没有人能够发布“快速修复，以后会提高安全性”(顺便说一句，这是我在过去看到的实际提交消息)，因为它不会通过自动化测试。

但是做到这一点很难。这就是为什么“DevSecOps”现在是想提高应用程序安全性的人的合法职业道路。问题是要学的东西太多了，几乎没时间学，对吧？

### 免费工具

在之前，我已经[写过关于 OWASP 的文章。用他们自己的话说:](https://dev.to/dotnetcoreblog/owasp---who-jck)

> 开放 Web 应用程序安全项目(OWASP)是一个 501(c)(3)的全球性非营利慈善组织，致力于提高软件的安全性。我们的使命是让软件安全可见，以便个人和组织能够做出明智的决策。OWASP 在向全球个人、公司、大学、政府机构和其他组织提供有关 AppSec 的公正、实用的信息方面处于独特的地位。OWASP 是一个由志同道合的专业人士组成的社区，发布软件工具和基于知识的应用程序安全文档

他们所做的一切(除了像 Global Appsec 这样的会议)都是免费的。免费培训资料，[免费聚会](https://www.owasp.org/index.php/OWASP_Chapter)，免费软件工具，免费知识分享。

*我说的“免费”是指“免费的啤酒，又名:不需要报酬*

不仅如此，还有各种关于应用安全的会议。有你可以选择的课程，也有你可以购买的书籍。你甚至可以通过使用像 [Kali Linux](https://www.kali.org/) 这样的东西来尝试自己成为 l33t hax0r

*Kali 是一个基于 Linux 的操作系统发行版，它是为渗透测试人员和白帽黑客设计的*

问题是，学习如何打败恶意的人需要大量的学习。当然，这些都是免费的信息，但是有很多这样的信息，要学习它们需要付出很大的努力。但是可以一点一点的学。

### 贵公司的应用是其信誉

想象一下，如果亚马逊遭遇大规模安全漏洞。比方说，他们所有客户 6 个月的所有订单都被泄露了。我们谈论的是姓名、地址、信用卡号、订单项目(人们订购的东西)，一切。

*我并没有说这已经发生了，我只是想制造一个例子*

除了每个人的数据被恶意的人获取的最初后果，以及一些人可能对他们的购物习惯感到尴尬成为众所周知的事情

说真的，你有没有买过很难向你爱的人解释的东西，更别说是朋友、陌生人，甚至是你的雇主了？

除此之外，亚马逊的声誉将会受到损害。他们会被媒体拖下水，不得不格外努力地编造对他们有利的故事。

不仅如此，AWS 是亚马逊的产品。有数百万(如果不是数十亿)的人每天都依赖 AWS 即使他们不知道。如果亚马逊被攻破，AWS 将是下一个。像 Capital One、BP、GE、Time 和 Philips 这样的公司将会受到冲击。更不用说 AWS 的杀手级应用:网飞。

想象一下，如果您的公司被成功攻击，并且您的所有客户数据被盗。它能挺过公关噩梦吗？

这甚至还没有触及到像 [GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) 这样的事情，以及你需要遵守的其他规定。想象一下，如果你的公司不得不抵御 GDPR 的罢工。无论你的产品和服务有多好，都没有回头路可走。你的顾客会离开。

这就是应用程序安全性对您的公司如此重要的原因。至少，从企业主的角度来看是这样的。