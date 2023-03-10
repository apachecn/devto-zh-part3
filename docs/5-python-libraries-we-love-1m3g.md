# 我们喜爱的 5 个 Python 库

> 原文：<https://dev.to/nylas/5-python-libraries-we-love-1m3g>

[![python_libraries_hero](img/62d0d5c7413045761e760b00d4d08f6c.png)](https://www.nylas.com/blog/5-python-libraries-we-love)

Nylas 是一家 Python 商店，虽然我们使用了许多您会在电子邮件 API 代码库中看到的 Python 库，如 datetime、imaplib 和 xml 解析器，但我们也使用了大量标准库之外的开源 Python 工具。这些重要的库使我们免于一次又一次地重复发明轮子，并且我们已经从中获益良多。

开源代码不仅仅是被开发出来并一直存在；它由有才华的专门 Python 开发人员定期维护和扩展。[Nylas Sync Engine](https://github.com/nylas/sync-engine)，我们用于同步来自 Gmail、微软和 IMAP 电子邮件提供商的数据的核心服务，最初是一个开源项目，它让我们对 Python 维护者有了独特的欣赏，他们对问题做出响应，合并拉请求，并迭代我们每天使用的开源工具。

以下是我们最喜欢的 5 个 Python 库，以及我们如何使用它们:

### **1。快速的 pt python&简单的内部工具**

当你运行和我们一样多的服务时(同步服务、API、开发者仪表板和 webhooks 服务，仅举几例！)，由于数百个客户都以独特的方式使用您的平台，并且数据库分布在 200 个碎片上，所以在互连的系统上迭代和调试问题变成了一个复杂的问题。

[ptpython](https://github.com/prompt-toolkit/ptpython) REPL 为 python shell 带来了自动完成、语法高亮和多行编辑功能，创建了一个健壮的可定制的 Python 环境。我们使用它来增强 Python 控制台，为我们的开发人员和开发成功工程师提供预加载的文本、帮助函数和对象。如果日志中出现错误，我们可以在 Python REPL 中快速加载有问题的对象，调试根本原因，并直接在 Python shell 中试验解决方案。

为了好玩，我们把它做得很漂亮:

[![Screen Shot 2018-11-28 at 11.00.51 AM](img/671058111c89010de777121738e4ff65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DpzzoYz_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/Screen%2520Shot%25202018-11-28%2520at%252011.00.51%2520AM.png)

### **2。exchangelib 用于同步来自微软的数据**

当我们同步数据时，我们使用许多不同的电子邮件协议。对于[微软](/exchange-email-api)账户，我们与 Exchange ActiveSync (EAS)和 Exchange Web Services (EWS)同步数据，并且在我们的 EWS 集成中使用 [exchangelib](https://github.com/ecederstrand/exchangelib) 。微软的文档很难通过谷歌搜索找到，也没有直观地组织起来，exchangelib 特别擅长注释代码，以包含相关文档的链接，因此我们很容易找到更模糊属性的描述。

[![python_libraries_support](img/65ef92879876404160997f8d0d6fc3ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YjY6d1tE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn2.hubspot.net/hubfs/3314308/python_libraries_support.png)

### **3。解析 URL 的 tldextract】**

如果你曾经尝试过编写一个正则表达式来定义一个有效的 url，你就会知道这并不像听起来那么简单。普通的`.com`、`.org`和`.net`顶级域名很容易编纂，但如今`.limo`、`.pizza`和`.duck`也都是有效的后缀。在我们的代码中，我们需要解析 url 来验证电子邮件和服务器地址，所以我们使用 [tldextract](https://github.com/john-kurkowski/tldextract) 将协议与给定 URL 中的域、子域和后缀分开。

### **4。用于快速、可重复使用的测试夹具的 vcr**

在 Nylas，我们使用很多 API，这意味着我们会发出很多 HTTP 请求。为了简化和加速测试，我们使用 [vcr](https://github.com/kevin1024/vcrpy) 来记录测试代码中 HTTP 交互的响应，并将它们保存到代码库中的一个平面文件中，称为卡带。

在初始响应被记录之后，它会被后面的测试“重放”,所以我们不必在测试代码中对外部 API 发出实时请求。这使得我们的测试快速(不再有真正的 HTTP 请求)、确定性(测试将继续通过，即使您离线)和准确(响应将包含与您从实际请求中获得的相同的头和体)。

### **5。有序缓存的 expiringdict】**

Mailgun 的 [expiringdict](https://github.com/mailgun/expiringdict) 是做一件事的库的一个很好的例子，而且做得很好。它们的数据结构基于集合的“T0”，创建一个具有最大长度的字典，以及在一定时间后“过期”的项目，用作缓存。

我们的数据库中有大量的数据，缓存我们知道经常执行的查询的结果可以让我们提供更快的结果，同时减少数据库的负载。为了防止缓存变得太大，从而影响性能，我们喜欢限制大小，并且为数据设置到期日期很方便，这样我们的缓存就不会过时。

Python 是我们工作的核心，我们对 Python 的热爱不仅限于我们的工作；你还可以发现我们正在构建 Python 的附带项目，在 T2 的 PyCon 上发表演讲，并参加我们在当地的聚会。你有一个有趣的 Python 库想要分享吗？我们很想听听！

关于如何开始使用 Nylas APIs 的更多信息，[点击这里](https://www.nylas.com/products/)。

[![](img/bd542fef67fe06aaddff3bde9f278be7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a5CdMGz1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://track.hubspot.com/__ptq.gif%3Fa%3D3314308%26k%3D14%26r%3Dhttps%253A%252F%252Fwww.nylas.com%252Fblog%252F5-python-libraries-we-love%26bu%3Dhttps%25253A%25252F%25252Fwww.nylas.com%25252Fblog%26bvt%3Drss)