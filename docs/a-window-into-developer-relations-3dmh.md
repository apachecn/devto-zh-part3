# 开发商关系的窗口

> 原文：<https://dev.to/bengreenberg/a-window-into-developer-relations-3dmh>

我记得我第一次听说开发者关系是一条可能的职业道路。我在新奥尔良的 RubyConf 参观赞助商区的展位。正是在那里，我第一次见到了一个被称为“开发者福音传道者”的人。这是什么工作？作为一名拉比出身的程序员，我并不热衷于接受一个带有*福音传道者*字样的标题，但我对更广泛的开发者关系领域很感兴趣。

快进到现在，我在 [Nexmo](https://www.nexmo.com) 的第一个开发者关系职位。作为一名 Ruby 开发者倡导者和 [Nexmo 开发者平台](https://developer.nexmo.com)的平台工程师，我参与了各种各样的活动。在 Twitter 和其他地方有一个正在进行的对话，关于开发者倡导者到底在做什么？开发者关系的工作是什么？开发者提倡的还是代码吗？我们只是旅行和参加会议吗？

请注意，这只是我的经验，我很高兴分享一个进入开发者关系世界的窗口。首先，让我谈谈我对开发者关系的理解:

*开发人员关系是指通过在内部宣传开发人员的需求来服务开发人员社区，并提供尽可能好的体验和工具来支持他们的工作，并作为他们成功的催化剂。*

那实际上每天是如何翻译的呢？

*   开发者体验
*   客户端库
*   开发者参与

## 开发者体验

开发人员与您的平台交互的方式有哪些方面可以改进？

开发人员能理解你的文档结构吗？不同经验水平的开发者呢？你的 API 参考反映了你的 API 的当前现实吗？你的代码样本是最新的并且容易理解吗？

如何让你的同事更容易在你的平台上迭代，让他们的工作更简单，从而更快地为你的社区带来新功能和新信息？

这些问题以及更多的问题激发了我的许多开发经验。开发人员的经验可以说是这项工作最重要的方面之一。开发者能快速直观地得到他们需要的东西吗？你的平台容易迭代吗？你的文档是最新的并且结构良好吗？

在过去的几个月里，我为改进我们的平台所做的一些工作包括向应用程序添加更多的测试以使其更加持久:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 追加 PHPInlinerFilter# 1333](https://github.com/Nexmo/nexmo-developer/pull/1333)的测试

[![benhayehudi avatar](img/4d49a4f979f5129d7133b082d9ed8834.png)](https://github.com/benhayehudi) **[benhayehudi](https://github.com/benhayehudi)** posted on [<time datetime="2018-12-26T07:57:42Z">Dec 26, 2018</time>](https://github.com/Nexmo/nexmo-developer/pull/1333)

## 描述

添加了对`PHPInlinerFilter`的测试:

*   将`?start_inline=1`添加到`php`字符串中
*   不对空白或非匹配字符串执行上述操作

## 部署笔记

关于部署所包含的工作的注意事项。这些应该记录任何数据库迁移等。

[View on GitHub](https://github.com/Nexmo/nexmo-developer/pull/1333)

创建配置驱动的可定制登录页面:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 可配置登陆页面:渲染开发者聚焦 YAML 配置 #1372](https://github.com/Nexmo/nexmo-developer/pull/1372) 

[![benhayehudi avatar](img/4d49a4f979f5129d7133b082d9ed8834.png)](https://github.com/benhayehudi) **[benhayehudi](https://github.com/benhayehudi)** posted on [<time datetime="2019-01-25T12:14:55Z">Jan 25, 2019</time>](https://github.com/Nexmo/nexmo-developer/pull/1372)

在此 PR 中，我们使用新的 developer spotlight 配置 YAML 文件，并使用其所有正确的内容块类型以及正确的行和列来呈现 Developer Spotlight 登录页面。

[View on GitHub](https://github.com/Nexmo/nexmo-developer/pull/1372)

添加文档以使该功能更易于使用:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 配置驱动登陆页面文档 #1439](https://github.com/Nexmo/nexmo-developer/pull/1439) 

[![benhayehudi avatar](img/4d49a4f979f5129d7133b082d9ed8834.png)](https://github.com/benhayehudi) **[benhayehudi](https://github.com/benhayehudi)** posted on [<time datetime="2019-02-11T13:12:07Z">Feb 11, 2019</time>](https://github.com/Nexmo/nexmo-developer/pull/1439)

## 描述

在这个 pull 请求中，我们为配置驱动的登录页面添加了文档，描述了流程和每个内容块。

[View on GitHub](https://github.com/Nexmo/nexmo-developer/pull/1439)

关于开发人员体验工作的重要一点是，有些工作是社区可以直接看到的，而其他工作是为了确保平台保持弹性，因此更具内部性。面向外部的改进和内部的改进对于开发人员的体验都是必不可少的。一个运行良好的应用程序意味着更少的停机时间，更快的结果和更好的交互。

## 客户端库

如果您想让人们尽可能简单地将您的 API 集成到他们的工作中，那么客户端库是必不可少的。然而，一个没有被很好地维护或者很好地记录的客户端库可能会带来更多的伤害。当您发布一个客户端库时，您还必须主动管理它。简言之，这意味着:

*   跟上语言的最新变化
*   使库与 API 变化保持同步
*   确保文件清晰且相关
*   及时响应问题和拉动式请求

作为这项工作的新手，我很幸运拥有一个由经验丰富的同事组成的团队，他们提供了这一领域的最佳实践范例。我非常支持对你需要学习的东西持开放态度。学习新的任务和新的做事方式没什么不好意思的。因此，当我致力于向我们的 Ruby 客户端库添加一个新的 API 服务时，关于如何最好地做到这一点的对话在 GitHub pull 请求中是公开可用的。也许我在这一领域的学习记录会对其他正在学习这一领域最佳方法的人有所帮助。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 增加了对对话 API   #117](https://github.com/Nexmo/nexmo-ruby/pull/117) 的支持

[![benhayehudi avatar](img/4d49a4f979f5129d7133b082d9ed8834.png)](https://github.com/benhayehudi) **[benhayehudi](https://github.com/benhayehudi)** posted on [<time datetime="2019-02-08T12:15:34Z">Feb 08, 2019</time>](https://github.com/Nexmo/nexmo-ruby/pull/117)

## 描述

这个 pull 请求将对 Conversations API 的支持添加到了 Nexmo Ruby 客户端库中。

对话 API 目前处于测试阶段，但它是一个稳定的测试版，并且已经被请求将其合并到客户端库中。

API 支持标准的 CRUD 操作，例如:

*   [创建对话](https://developer.nexmo.com/api/conversation#createConversation)
*   [列出对话](https://developer.nexmo.com/api/conversation#listConversations)
*   [更新对话](https://developer.nexmo.com/api/conversation#replaceConversation)
*   [获取特定对话](https://developer.nexmo.com/api/conversation#retrieveConversation)
*   [删除对话](https://developer.nexmo.com/api/conversation#deleteConversation)

[View on GitHub](https://github.com/Nexmo/nexmo-ruby/pull/117)

## 开发者参与度

你是那种喜欢离开办公桌和同事讨论工作的开发人员吗？你喜欢和社区里的其他人见面，分享工作中的成功和挑战吗？我肯定是这些人中的一员，开发人员参与的领域是我工作中最享受的方面之一。

开发者参与可以发生在会议、聚会、共同工作空间、堆栈溢出或 GitHub 上提出的问题。任何时候你将你的焦点、注意力和关心指向开发人员社区，你都在做开发人员参与的工作。我喜欢和使用我们 API 的人或者对它感兴趣的人交谈。我想知道什么在起作用，更想知道什么在不起作用。整合起来有什么困难？你有过哪些成功的经历？你希望那里有什么，但目前没有？

开发人员参与也是思考与更大的社区分享新见解和完成任务的最佳方式的行为。如果这项工作有助于社区的成功，那么让社区参与快速启动示例可以进一步推进这一目标，例如，[如何将音频流式传输到现有的电话通话中](https://www.nexmo.com/blog/2019/01/24/play-streaming-audio-to-a-call-with-ruby-dr/)。

这些对话和这个过程直接激发了我们未来的工作。我们所关注的、我们所优先考虑的以及我们所构建的东西都与社区的对话密不可分。

我的开发人员关系是大量的编码和大量的倾听。这是位于编程和移情交叉点的工作。它让我在工作中展现更完整的自我；我对工程的热爱，我对人际交往的热情以及我对教学的渴望。归根结底，开发人员关系是一个服务驱动的职业。你的工作是为开发者社区服务。你努力把他们的需求和愿望放在首位。当你促成并促进他们的成功时，你就成功了。作为一名前拉比，这部作品感觉既熟悉又重要。