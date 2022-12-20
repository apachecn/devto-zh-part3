# 威胁建模无服务器

> 原文：<https://dev.to/azure/threat-modelling-serverless-500k>

前几天，我和我当时的同事[布赖恩·休斯](https://twitter.com/nebrius)会面，讨论他为 JSConf EU 开发的**无服务器应用**的安全性(关于他的开发不会有剧透，别担心)。我们曾在一起出差时讨论过威胁建模的想法，他想尝试一下。由于[塔尔·梅拉梅德](https://twitter.com/_nu11p0inter)把我拉进了 [OWASP 无服务器十大项目](https://www.owasp.org/index.php/OWASP_Serverless_Top_10_Project)，我最近对无服务器应用特别好奇，所以我很兴奋有机会潜入这个兔子洞。

#### 布莱恩的应用架构:

*   Azure 功能应用程序(MSFT 无服务器)
*   JWT 令牌的 Auth，他们将是短命的
*   他的应用程序将允许其他 Azure 用户调用它，并带有参数，它将做一些令人兴奋的事情(见？不剧透！)

[![Bryan Hughes, South Korea, Demilitarized Zone DMZ](img/bd4cd6a3371c457cce85de1c200134e2.png "Bryan Hughes, South Korea, Demilitarized Zone")](https://res.cloudinary.com/practicaldev/image/fetch/s--n5MTbriA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d428lnqjd5ff2r43t40a.png)

###### 布赖恩·休斯，南朝鲜非军事区 DMZ

一旦布莱恩解释了他的应用程序将会做什么，他告诉我他的安全顾虑:谁能访问他的应用程序？他们能进入他的 Azure 订阅的其他领域吗？他应该使用哪种类型的认证令牌呢？他将如何处理会话管理？所有这些绝对是合理的担忧，我印象深刻！

我们讨论了他关心的每一个问题，以及减轻每一个风险的可能的技术解决方案。例如，只使用 JWTs 来发送随机的会话令牌值，从不发送密码或敏感数据，也从不发送实际上与重要内容相对应的数字，比如使用某人的 SIN 号作为他们的会话 ID 号，这是敏感信息和不安全的直接对象引用。我提醒他，jwt 是编码的，不是加密的，因此它们不是传输数据的安全方式。此外，我建议他围绕这个应用程序(防火墙)创建一个[虚拟网络](https://docs.microsoft.com/en-ca/azure/virtual-network/security-overview?WT.mc_id=devto-blog-tajanca)，以防有人进入，这将意味着他们无法进入他的网络和订阅的其余部分。

注意: [RFC 7516](https://tools.ietf.org/html/rfc7516) 允许加密 JWT 令牌，点击链接了解更多信息。

然后我们谈论了**我的担忧**，开始是向 Bryan 问了一堆关于他的用户和数据的问题。

*   你想从你的用户那里得到什么数据？有什么敏感的吗？

他要求他们的 GitHub 信息，这样他就可以让他们访问他的无服务器应用程序，这样他就可以授予他们访问权限，但仅此而已。这份数据是敏感信息。

*   你的用户是谁？他们使用你的应用的动机是什么？

这些用户是与会者，他们想学习如何调用像 API 这样的无服务器应用程序，然后让他的应用程序做它会做的很酷的事情。这是一个学习的机会，而且很有趣。

*   让我们假设你有一个恶意用户，他们如何攻击你的应用程序？

我首先担心的是拒绝服务或暴力式攻击。为了避免这些攻击媒介，他应该遵循 [Azure Functions 最佳实践指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-best-practices?WT.mc_id=devto-blog-tajanca)，具体来说，他应该将 maxConcurrentRequests 设置为一个较小的数字(以避免分布式拒绝服务)，通过启用“dynamicThrottlesEnabled”标志来添加节流(将请求减慢到一个合理的速度，这将停止脚本攻击)，并且最好还为 maxOutstandingRequests 设置一个较低的数字，以确保没有人溢出他的请求缓冲区，这也将导致拒绝服务。(注意这是 CIA:可用性中的“A”)

我担心的其他攻击是有人发送格式错误的请求，试图从他的应用程序中引出意外行为，如崩溃、删除或修改数据、允许用户注入自己的代码或其他潜在问题。我们讨论了使用白名单进行用户输入验证，并拒绝所有格式不正确的请求，或者包含任何不是“a-z，A-Z，0–9”的字符的请求。(注意这是对完整性和可用性的攻击)

我在这里列出的最后一个**攻击媒介**是用户可能试图访问数据本身，即所有其他用户的订阅 id(机密性)。这是这个列表中最重要的风险，因为你是这些数据的守护者，如果你丢失了这些数据，并且他们因此被成功攻击，这可能会导致灾难性的声誉损害(对会议，对他作为应用程序的创造者，对他的雇主微软)。当我解释这一点时，确保他的用户及其数据在使用他的系统期间和之后得到保护成了他的首要任务。

[![Tanya Janca, South Korea, DMZ, 2019](img/1a5bc3b5061dfde3ab5284586bb90c7e.png "Tanya Janca, South Korea, DMZ, 2019")](https://res.cloudinary.com/practicaldev/image/fetch/s--7x-HDPoE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gsnuuofphm0c3da11zye.jpeg)

###### Tanya Janca，韩国，非军事区，2019

*   您将这些数据保留多长时间？你把它存放在哪里？你是怎么储存的？

最初 Bryan 希望避免一起使用数据库；没有数据收集意味着没有东西可偷。尽管他仍在研究这是否有可能，但目前他的计划是使用数据库。

他决定将这些数据保存到会议结束后，然后全部销毁(因此这种风险只有 48 小时)。它将存储在数据库中(我们讨论了静态和传输中的加密，以及总是使用参数化查询，并为调用这些查询的数据库用户应用最小特权(可能是只读或读/写，但不是 DBO)。

*   这个会议在哪个国家举行？你会服从 GDPR 吗？

它将在欧洲，因此受 GDPR。我把他介绍给了 Miriam Wiesner，她是 MSFT 的一名员工，有着测试和安全评估的背景，碰巧住在欧盟，因此应该很熟悉。我说她会比我有更好的建议。

谈话持续了大约一个小时，但我想你已经明白了。
无服务器的关键是记住，几乎所有相同的 web 应用程序漏洞仍然适用，例如注入或拒绝服务(DOS)攻击，仅仅因为不涉及服务器，并不意味着您不需要注意应用程序的安全性。

如果你想跟上布赖恩·休斯，看看他的项目成果，你可以在 Dev 上关注他。到。

我希望这个非正式的威胁模型对你有所帮助。

**关于这一点和更多，请查看我的书，[爱丽丝和鲍勃学习应用安全](https://aliceandboblearn.com/)和我的在线培训学院，[我们黑紫色](https://academy.wehackpurple.com)！**