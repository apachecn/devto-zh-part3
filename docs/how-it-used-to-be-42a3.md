# 过去是怎样的

> 原文：<https://dev.to/derickson82/how-it-used-to-be-42a3>

我想一开始就说清楚一件事。我不是老前辈...还没有。自 2006 年以来，我一直是一名专业软件工程师，并计划长期坚持下去。我看到了一些事情。我做过一些事。可怕的，值得畏缩的事情。我现在喜欢嘲笑他们，所以我想分享一些我的故事。

我当时在一家小型软件商店工作，制作与 Novell 产品集成的 CRM 软件，尤其是 Groupwise。我在一个由两名开发人员组成的团队中:一名负责桌面客户端的 Delphi 程序员和我，负责 Java web 应用程序。(在那些日子里，“桌面”仍然意味着在你自己的操作系统上本地运行的应用程序，而不仅仅是全屏模式的 web 应用程序。)

为了明确架构，我们有一个胖客户端，运行在每个人的桌面上，直接与数据库通信。没有中间层。让我再说一遍....不...中间..层。java web 应用程序是在该产品开发的后期才出现的，所以它没有公开供桌面客户机使用的 API。

这也无妨，因为我们并没有与 Groupwise 进行服务器端的集成。他们根本没有公开服务器端 api。桌面应用程序从随 Groupwise 一起安装的 COM 对象中访问您的电子邮件和日历等内容。这在当时是相当标准的。

有趣的部分来了。我必须与 web 应用程序进行相同类型的集成。所以我编写了 javascript(和一些 vbscript ),它将访问您机器上的 COM 对象(需要特殊权限)来读取您的电子邮件、联系人和日历，将这些信息发送到 java web 应用程序进行处理，然后将这些信息放入 JSP 页面中进行呈现，然后将其发送回浏览器。

那一年，在 Novell 的 BrainShare 会议上，他们宣布了一个服务器端集成选项，使用现有的最好的 web api，带有一个 SOAP 接口，我们很高兴有这样一个选项。激动万分！

不用说，现在的情况与不久前的情况大不相同。我很幸运大部分时间都在使用较新的技术，但我也非常清楚有大量的人仍然在使用这种技术，而且年龄要大得多。对于那些被困在过去的可怜灵魂，我向你们表示感谢。你让世界不停转动。