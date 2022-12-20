# 仿冒我的公司。企业的信息安全课程

> 原文：<https://dev.to/browserlondon/phishing-my-company-an-infosec-lesson-for-businesses-3hci>

赢得新客户不可或缺的一部分是他们对你专业知识的信心，而成为专家的最佳途径是从经验中学习。随着网络钓鱼等网络威胁的增加，信息安全(infosec)现在是我们从事的大多数项目的先决条件；这是顾客最关心的问题。

为了客观地看待这个问题，美国国税局报告称，2018 年网络钓鱼攻击激增 [60%，而网络钓鱼实验室(PhishLabs)的](https://www.forbes.com/sites/kellyphillipserb/2018/12/04/irs-warns-on-surge-of-new-email-phishing-scams/#6766b6804b24) [2018 年网络钓鱼趋势报告](https://info.phishlabs.com/hubfs/2018%20PTI%20Report/PhishLabs%20Trend%20Report_2018-digital.pdf)强调，针对 SaaS 系统的攻击增加了 237%。

作为回应，有一个明确的转变，即通过宣传活动和来源，如[观看你的黑客](https://watchyourhack.com/)，教育更广泛的公众。坦率地说，我认为这种教育过程不会很快发生——我采访的大多数高管客户都知道黑客行为及其可能造成的损害，但当他们的收件箱收到钓鱼邮件时，他们仍然无法识别出泄露秘密的迹象。

## 能做什么？

技术过滤器可以在一定程度上防御网络钓鱼攻击，但很大一部分安全负担仍然落在个人身上，这就是教育的重要性。

<figure>[![Screen shots of Google Gmail and Microsoft Office 365 phishing warnings](img/6eb9306acf6a5aa7b64e32e7fa2de01a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LUZMb0nu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/02/Google-and-outlook-phishing-warnings-1024x569.png)

<figcaption>Gmail(上图)和 Office 365(下图)中的技术过滤器可以证明是有效的，但应该得到员工培训的支持。</figcaption>

</figure>

这就是为什么在过去的几年里[我一直支持](https://www.browserlondon.com/blog/2018/05/04/protecting-yourself-and-your-data-on-the-web/)我们的信息安全部门进行红旗演习和安排教育研讨会，以帮助保持我们的准备水平。这样做的目的是让我们一起工作的每个人都知道在发生事故时应该注意什么、做什么以及何时寻求帮助。

现在它已经融入了我们的日常生活。此外，在与客户合作构建、设计和[构建他们的数字解决方案](https://www.browserlondon.com/case-study/portal/)时，这已经成为我们战略方法的一部分。除此之外，我们还能够通过对他们的团队和利益相关者进行网络威胁方面的教育来提供附加值。

但是，我跑题了，让我们回到这篇文章的主题。

## 消失的网络钓鱼

因此，我的轶事证据表明，大多数高管客户不知道如何从真正的邮件中识别出钓鱼邮件，但我想进一步探索这一理论。有谁比我自己不知情的公司更适合测试它呢…

我们一年两次的 JAMS 黑客马拉松给了我几天的时间来计划这项活动，在一位值得信赖的网络合作伙伴的帮助下，我们设计了一系列网络钓鱼骗局，旨在测试浏览器团队的准备情况。

<figure>[![A person types on a laptop set on a table](img/b1a3a71c63a2c9be195c99ccee6e8edd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---aN7qWbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2018/12/JAMS-9-1024x683.jpg) 

<figcaption>我们花了两天的时间来构建测试。我不得不撒谎我在做什么，并为为什么在黑客马拉松结束时我没有任何东西可以展示给团队而找借口。</figcaption>

</figure>

在两天的拥堵结束时，我们对提议的测试感到满意。我们将一个电子邮件服务器列入白名单，设计了一封假的 HTML 电子邮件(旨在密切模仿我们的第三方数字服务之一)，创建了一个假的登录页面，并建立了一个数据库来记录所有泄露的信息。一开始，我们只是希望记录我们公司中谁打开了网络钓鱼电子邮件，但在最终上线时，我们添加了额外的数据点，包括:

*   电子邮件是否已发送给个人
*   个人是否点击了电子邮件中的任何链接
*   个人在登录页面中输入了哪些敏感信息

我真正想知道的是，在之前的红旗演习、教育研讨会和讲座中，我的团队听得怎么样。因此，作为测试的一部分，我还跟踪了:

*   谁向信息安全官(ISO)报告了此事
*   他们花了多长时间向 ISO 报告
*   事件是否升级，升级对象是谁
*   采取了什么步骤来查明是否有人输入了敏感信息
*   如果一名工作人员输入了敏感信息，该如何处理

### 那么，团队表现如何？

从测试中获得的结果令人着迷。

即使在我们精通技术的开发人员和设计师团队中，这封电子邮件(来自一个看似合理但不完美的发送地址)仍然设法欺骗了 36%的收件人点击进入登录页面。

令人高兴的是，团队中有 20%的人认为这封邮件可疑，并迅速向更广泛的群体发出了警告。顺便说一句，我观察到我们的办公室团队很容易口头警告对方这个消息。另一方面，我们的远程员工感觉风险更大一些。他们被物理隔离，因此警告依赖于他们在打开钓鱼邮件之前阅读他们的垃圾邮件或警告电子邮件。

<figure>[![Test scores showing the % of the browser team that opened the phishing email](img/4828c149ab49b5448837cfd976e58f5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J-9bAFfJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/02/Test-scores.jpg) 

<figcaption>尽管是一个精通技术的开发团队，我们的成绩并不完美</figcaption>

</figure>

### 看什么

测试结束后，我与那些识破骗局的人交谈，问他们注意到了什么，让他们警告团队。大多数情况下，这封邮件提到的文件有一个似乎不可信的标题(浏览器圣诞节计划)；这听起来不像是我们实际创建或共享的文档。这引起了怀疑，然后，当他们仔细查看链接目的地 URL 和发送地址时，这些怀疑得到了证实。

[![An email from a browser team member explaining how he caught the phishing email](img/f559cc6b0df4a04902debbb8a15e4a85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P2_4wjRy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/02/email.png)

如果我选择了一个稍微不那么异想天开的文档名称，更多的人可能会被骗去点击并输入他们的详细信息到我们伪造的登录页面。

总的来说，这些结果描绘了我们当前的准备情况，但更重要的是，它们还强调了培训可以帮助团队保持准备状态的新领域，并让他们知道在发生事故时应该注意什么、做什么以及何时寻求帮助。

各种形式和规模的公司越来越依赖技术来支持他们的业务。与此同时，网络威胁在持久性和严重性方面都在增加，而且不会消失；网络安全正迅速成为最受欢迎的服务之一。无论是开发人员、财务人员还是管理人员，每个人都将从培训中受益，而网络钓鱼测试是向团队成员介绍网络威胁的绝佳方式。当谈到信息安全时，每个公司都需要将准备工作放在首位。

## 接下来是什么？

我做了一个演示，详细介绍了结果，并附上了我们正式协议的大纲。这篇教育文章之后是一份基于场景的调查问卷，以帮助个人学习和发现网络攻击。它还强化了我们在信息安全、协议以及如何降低工作之外的风险的技巧方面的立场。点名羞辱对谁都没有帮助，但教育有帮助。使用它来保持准备状态，并保持安全，以应对网络攻击。

如果你对网络意识以及我如何与我们的网络合作伙伴开展这项活动感兴趣，请随时通过 Twitter @ renemorency 联系我。

* * *

帖子[仿冒我的公司。一堂商业信息安全课首先出现在伦敦的 T2 浏览器上。](https://www.browserlondon.com/blog/2019/03/05/phishing-company-infosec/)