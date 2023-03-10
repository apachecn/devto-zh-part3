# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-1o4c>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

**[我不是真正的程序员](https://dev.to/andygeorge/i-am-not-a-real-programmer-1ogo)** 这篇文章是对抗冒名顶替综合症的精彩且完全相关的尾巴。 [@daveskull81](https://dev.to/daveskull81) 加入更多鼓励和观点:

[![daveskull81 profile image](img/8ebcd85d89f987dbae0eb7a0f29c07aa.png) ](/daveskull81) [ dAVE Inden ](/daveskull81) • [<time datetime="2019-01-26T02:52:19Z" class="date-short-year"> Jan 26 '19 </time>](https://dev.to/daveskull81/comment/8c7o) 

谢谢你张贴这个。我完全同意。如果有人编写代码，让计算机做任何事情，他们就是程序员。我相信这对很多人来说都是真实的。我知道，作为一个自学成才的程序员，我正在努力进入一个开发人员的角色，我患有冒名顶替综合症。但是，重要的是要记住，这些感觉在开发人员中比我们想象的要普遍得多，尤其是那些有以此为生的经验的人。

[@joelnet](https://dev.to/joelnet) 在 **[中跟进了一个很棒的初级读本和一组警告，小心 JWT 炒作列车](https://dev.to/darraghor/be-careful-of-the-jwt-hype-train-3e81)** ，还有一些额外的想法和考虑:

[![joelnet profile image](img/d77df0cc67c87968a2f6a59e88575a60.png) ](/joelnet) [ JavaScript Joel ](/joelnet) • [<time datetime="2019-01-23T18:59:22Z" class="date-short-year"> Jan 23 '19 </time>](https://dev.to/joelnet/comment/8al6) 

> 我认为有太多的炒作，人们使用 JWT 是因为它有光泽！

随着我们的行业将我们的范式从整体服务转向微服务，了解 JWT 很快成为一项要求。有时候，用这个闪亮的新玩具作为学习的借口是很好的。即使你的应用程序每分钟服务的请求少于 4000 个，JWT 对你的应用程序来说是多余的，学习 JWT 并拥有它的实际知识也是有价值的。

> 许多文章将向您展示如何设置和登录 JWT，但他们忽略了困难的部分-注销用户和黑名单用户

这是一个肯定被忽视的领域，也是一个复杂的领域。最常见的解决方案是在 auth 服务器上，它会保存一个经过身份验证的用户列表，在用户注销时过期。对于每个请求，需要对照认证服务器检查令牌，以查看令牌是否仍然有效。这大大增加了开销。

更常见的是，大多数公司会减少 JWT 的到期时间，因此必须更频繁地刷新它。这是由于管理真正的注销体验的困难和开销。

> “JWT 是安全的”

JWT 是安全的，但同时它不如基于会话的认证安全。例如，JWT 更容易被劫持，必须设计成防止劫持。未到期的 JWT 可能成为安全风险。

您还相信令牌签名不会被泄露。如果您使用的是弱加密、将来会变得脆弱的加密，或者私钥被泄露，就会发生这种情况。会话不存在此漏洞。

因此，虽然 JWT 是安全的，但它也引入了新的攻击媒介，需要加以考虑。

使用 jwt 有充分理由:

*   API 后端——你的站点是静态的，你的后端是一个 API。
*   微服务架构——一种非常常见的跨断开连接的系统进行身份验证的方式。
*   学习 JWTs 在较小的项目中实现 JWT 是开始学习 JWT 的好方法。
*   将您的身份验证外部化到第三方提供商，如 Auth0。

jwt 并非没有自身的复杂性:

*   安全更复杂，需要理解。
*   就像微服务如何增加复杂性一样，JWT 增加了与认证断开相同的复杂性。
*   像注销这样简单的事情变得复杂，可能需要改变您的期望和业务需求。
*   做好 JWT 是困难的。

这是一个非常有趣的话题— **[“编码者/语言适合度”是什么](https://dev.to/ben/what-is-your-coderlanguage-fit-nn7)** 。 [@rpalo](https://dev.to/rpalo) 描述了为什么 Ruby / Python 会点击他的大脑:

[![rpalo profile image](img/d2df79a7640554be24029a559e2d464e.png) ](/rpalo) [ Ryan Palo ](/rpalo) • [<time datetime="2019-01-25T16:39:37Z" class="date-short-year"> Jan 25 '19 </time>](https://dev.to/rpalo/comment/8bpm) 

Python 和 Ruby 是我最喜欢的两个。对 Bash 的荣誉奖，因为它很有趣。:)

我尝试过学习更严格的静态、编译语言(我会继续尝试，因为我认为这是一项重要的技能)，但我无法像在 Python 和 Ruby 中那样高效或有创造力。我喜欢你可以把一些代码扔进一个文件，然后马上运行它。我喜欢他们对小事的宽容。

> 我喜欢它们没有分号和最少的代码括号。#大括号前的空格# butalsoendkeywords

他们*没有*庞大、不透明、令人生畏的构建工具链，他们有一个强大的标准库，可以减少你安装依赖项的频率，这意味着当你*安装依赖项*时，它不会安装其他人依赖项的*整个世界*。

有趣的是，他们的哲学如此不同:

*   Python:应该有一种方法——最好是*只有*一种方法来做好任何给定的事情。
*   Ruby:做事情有很多好方法(方法别名，有人吗？)，而无论哪种方式让你开心都是你应该做的。

这两件事都引起了我的共鸣，但方式和情况不同。

如果 Python 像 Ruby 一样有块和对方法链接的关注:

```
numbers.filter(&:even?).map(&:to_s).join   # happy sigh 
```

Enter fullscreen mode Exit fullscreen mode

我会很高兴的。

我*真正*感到自己缺少的唯一一件事是一种简单的方法，可以重复地将我的代码部署给其他人，而不必让他们经历一堆毫无意义的步骤，因为他们不使用 Python。

但是不管我多久去学一门语言，我总是发现自己又回到了 Python 和 Ruby。它们真的是我曾经写过一些东西，然后坐下来微笑的唯一语言，因为代码是如此的光滑和漂亮。

不管怎样...我❤️ Python 和 Ruby。

我最喜欢的一些交流发生在 [#help](https://dev.to/t/help) 和 [#explainlikeimfive](https://dev.to/t/explainlikeimfive) 标签中。干净的呼叫和回复是一件令人愉快的事情。 **[什么是 SaaS 和 PaaS？](https://dev.to/wiz_ar/what-are-saas-and-paas-lk3)** 收到 [@jsrn](https://dev.to/jsrn) 的精彩回答:

[![jsrn profile image](img/e62a5cc2284c234ffbc279ae8bc6cfef.png) ](/jsrn) [ James ](/jsrn) • [<time datetime="2019-01-25T11:55:33Z" class="date-short-year"> Jan 25 '19 </time>](https://dev.to/jsrn/comment/8bli) 

SaaS ==软件即服务。基本上是云托管软件、网络应用等。通常伴随着经常性费用。

平台即服务。想想像 Digital Ocean、Microsoft Azure、Amazon Web Services 这样的提供商，他们提供虚拟服务器、数据库托管等。

您可以将 docker 或 kubernetes 与您选择的 PaaS 提供商一起使用，但不是必须的。Docker 和 Kubernetes 都是“集装箱化”应用软件。这实质上使您的应用程序更容易部署到不同的平台，因为您可以标准化与您的应用程序相关的环境部分。这是另一篇文章的主题。

后端即服务。我承认我以前没听过这个词。BaaS 提供商提供的服务可以让您轻松存储数据、文件、处理推送通知等。假设您没有任何他们的 API 无法满足的需求，这意味着您可以完全专注于您的前端代码，与他们的 API 集成，并且他们处理您的应用程序的大部分后端。

最后，当被问到: **[在工作岗位上工作一整天后，你还有精力和时间去实现个人目标吗？](https://dev.to/ilonacodes/do-you-have-any-energy-and-time-for-your-personal-goals-after-a-full-day-of-work-at-your-job-nm5)** [@mortoray](https://dev.to/mortoray) 讨论动机、设定目标、从个人项目中汲取能量:

[![mortoray profile image](img/3f601f0d3e93536df22a4b2d92f1094d.png) ](/mortoray) [ edA‑qa mort‑ora‑y ](/mortoray) • [<time datetime="2019-01-28T10:10:55Z" class="date-short-year"> Jan 28 '19 </time>](https://dev.to/mortoray/comment/8d5n) 

如果我没有追求我的个人目标，我将会没有生活的动力。尽管看起来很难，但你的个人目标才是最重要的。如果你把一切都献给工作，而这不是你的个人目标之一，你会发现自己精疲力尽，处于一个糟糕的境地。

和运动差不多。每天锻炼不是一项可有可无的活动。如果你不把它算进去，你会吃苦头的。你是否认为你有时间或精力并不重要，你不可能避免它并保持健康。我发现个人目标也是如此。

当然，我有时会精疲力尽，而且时间也少得可怜。但唯一值得的是我在追求自己的目标。

下周见，更多精彩评论，✌