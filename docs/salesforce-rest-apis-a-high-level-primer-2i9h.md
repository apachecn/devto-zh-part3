# 什么是 REST API？高水平的初级读本

> 原文：<https://dev.to/katiekodes/salesforce-rest-apis-a-high-level-primer-2i9h>

***注意:这篇文章是为 Salesforce 业务分析师和管理员写的，但即使你不使用 Salesforce，你仍然可以通过阅读它对“API”的用途有一个相当好的了解。**T3】*

Salesforce 人员:您是否被告知您可能需要一个“ **REST API** ”来**集成**外部数据和 **Salesforce** ？

如果你感到困惑，你有好的公司，因为它在不同的上下文中有不同的意思！

请继续阅读 Salesforce 中短语“REST API”的所有 3 种变体的“30，000 英尺视图”。

* * *

## 3 大概念，都称为“REST APIs”

### 1:在 Salesforce 内部构建您自己的“端点”

当人们提到“在 Salesforce 内部构建 REST APIs”时，他们指的是使用“Apex”语言对特定的 Salesforce 数据库(“org”)进行编程，以**接受来自外部世界**的请求**，从而通过 web 进行计算机对计算机的数据传输对话。**

数据可能会双向流动(一秒钟内更多)，但只能通过外部世界敲您的 Salesforce org 的门并说，“嘿，我有另一个通信请求！”以您所说的方式与您的 Salesforce 组织进行沟通。

这就是“编写 API”的意思——它意味着使用代码来定义一台计算机愿意如何被敲门和被交谈。

[![Monty Python's French Taunter blowing a raspberry](img/c4c1a1e204df361f16cd23a76cc42da1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0esQwZVo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/frenchtaunter.jpg)

当您在 Salesforce 中“构建 REST API”时，您就在您的堡垒中构建了一扇门，并在门旁设置了一名守卫，该守卫对她如何与另一边的陌生人进行交互保持谨慎。

*   您可以对您的 Salesforce 组织进行编程，以了解如何处理来自外部世界的数据，但只有在正确交付时。如果您的 API 在现实世界中，它可能看起来像这样:

> “如果你有给堡垒的东西，就把它装在一系列 12×12×12 的粉红色盒子里，只把第一个盒子涂成绿色条纹，最后一个盒子涂成紫色圆点，把你的名字和回信地址都写在上面。如果我们喜欢它们，我们会想出如何处理盒子里的东西。完成后，我们会在门上贴一张收据，告诉你我们是干净利落地处理了它们，还是烧掉了它们。”

*   或者，您可以对您的 Salesforce 组织进行编程，将数据从堡垒内部分发到外部世界，如果要求正确的话。如果您的 API 在现实世界中，它可能看起来像这样:

> “点击‘刮脸理发二位码’，然后是你的姓名和回信地址的莫尔斯电码，以及你想要的数据的描述。我们会用联邦快递给你一份清单，或者用联邦快递给你一张便条，说明你要求我们不愿意给你的数据。”

*   第三，你可以为**和**两个方向的数据传输建立通信协议，以真正促进双向对话。
    *   不过，请注意，那次对话的每一部分都是由某个“局外人”软件以特定的方式敲开你堡垒的门发起的，**无论“你关心的数据”以何种方式**流动。

### 2:Salesforce 内部的“端点”，由 sales force 构建

还有一个[通用 API](https://trailhead.salesforce.com/en/content/learn/modules/api_basics/api_basics_rest) ，内置于现有的每个 Salesforce 数据库(“org”)中，使用“REST”通信协议。

Salesforce 定义并维护其工作方式。

这是一扇*sales force corporation*切入你的堡垒并在那里设置守卫的门。

这个“API”就是像[Data Loader](https://help.salesforce.com/HTViewHelpDoc?id=data_loader.htm)&[Data Loader . io](https://dataloader.io/)&jitter bit&za pier 这样的“ETL”工具的作者在写这样的工具之前仔细研究的。

从技术上讲，您可以利用这个协议编写自己的工具，但是大多数人都不愿意这么做，因为其他公司已经做了这么好的工作。

*   在 Pardot 和营销云的世界中，第三方工具更难找到，所以使用方法 2 和针对他们提供的 API 编程是很常见的。
    *   这就是我的[“用于 Pardot /营销云的 Python”帖子](https://katiekodes.com/tags#api)到目前为止所做的。
*   如果您选择方法 1，与方法 2 或第三方工具相比，您有时可以在更少的“API 调用”中插入更多的数据。

第 1 和第 2 种方法都让您有机会在 Salesforce 数据库中编写代码，根据您的业务需求对传入数据进行后处理。

*   对于方法#1，您可以将“数据后处理逻辑”捆绑到 API 的后端以提高效率。
*   对于方法#2，您必须使用“流程构建器”、“工作流”、“流程”和“触发器”等来处理任何“数据后处理逻辑”

### 3:“呼叫”住在 Salesforce 之外的第三方“端点”

最后，您可以在您的 Salesforce 组织内编写 Apex 代码，敲开其他堡垒的大门。

这样做可能涉及编写使用“REST”协议和其他人的“API”的 Apex，但这与我们讨论的第一个概念完全不同。

当学习如何做到这一点时，您会经常听到类似“编写[异步 Apex](https://trailhead.salesforce.com/en/content/learn/modules/asynchronous_apex) ”和“用 Apex 生成 [HTTP 请求](https://trailhead.salesforce.com/en/content/learn/modules/apex_integration_services/apex_integration_rest_callouts)”的术语

* * *

## 它们在生产中是如何使用的

### 方法#1 & #2 有很多共同点。

两者在您的 Salesforce“堡垒”中都有一个“端点”

两者都需要相似的开发者技能水平。

*   #1 可能更受您的内部 web 开发人员的青睐。
*   #2 可能是您的内部数据库开发人员在阅读官方 Salesforce 文档时偶然发现的。

要决定哪种方法更适合您的业务需求，请扪心自问:

1.  您是否希望受限于使用 Salesforce 的“Apex”语言进行编程，但对 Salesforce 数据库性能有更多的控制？您可能更喜欢方法#1 *(“构建自己的 API”)*。
2.  您是否希望对数据集成编程语言有更多的控制？您可能更喜欢方法#2 *(“使用构建的 API sales force”)*。

**专业提示:**如果你使用方法#1 *(在你的堡垒墙上凿洞)*，确保你的非 Salesforce 内部程序员知道这些“端点”的存在，这样他们就不会浪费时间使用方法#2 重新发明轮子！

### 大多数日常 Salesforce 数据集成采用方法 2 的形式，但您不会知道。

如果您曾经使用第三方的“ETL”工具将外部数据连接到您的 Salesforce 组织，您间接利用了方法#2。

然而，您可能还没有在业务层面上将它称为“使用 API”

因此，这个笔记是一个有争议的点/书呆子琐事。

### 安全性:您组织中的“应用程序”&“软件包”

从安全的角度来看，您必须知道:

1.  您在 Salesforce 组织中安装的一些“应用程序”可能使用“方法#1”作为他们交付给您的软件包的一部分*(也就是说，他们可能会在您的堡垒上挖一个洞并设置一个守卫)*。
2.  您在 Salesforce 组织中安装的一些“应用程序”可能使用“方法#3”作为他们交付给您的软件包的一部分*(也就是说，他们可能代表您去敲第三方的门)*。

* * *

## 外卖

当与开发人员谈论 Salesforce 集成和 API 时，问他们它们是什么意思！

*   他们的意思是在你的 Salesforce 组织门口提出“敲门”请求吗？
    *   如果是这样，他们是计划自己打开你的堡垒，还是使用已经建立的销售队伍？
*   他们是指对你的 Salesforce 组织进行编程，让它去敲其他堡垒的门吗？

这些问题将有助于您在规划重要的体系结构和安全决策时，确保团队中的每个人都相互理解。