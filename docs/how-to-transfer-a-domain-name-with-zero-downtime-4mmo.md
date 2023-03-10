# 如何在零宕机的情况下迁移域名

> 原文：<https://dev.to/nickjj/how-to-transfer-a-domain-name-with-zero-downtime-4mmo>

**本文最初发布于 2018 年 10 月 9 日:[https://nickjanetakis . com/blog/how-to-transfer-a-domain-name-with-zero-down](https://nickjanetakis.com/blog/how-to-transfer-a-domain-name-with-zero-downtime)**

* * *

在之前的一篇文章中，我谈到了将我的一些域名从 internet.bs 转移到 Namesilo。

那篇文章谈到了我为什么要迁移，以及一旦我的主域名([nickjanetakis.com](https://nickjanetakis.com))即将到期，我将如何进行迁移。这一时刻已经到来。

这篇文章将回顾我是如何零停机转移我的域名的，这对我来说非常重要，因为我的整个业务依赖于域名解析。

不仅仅是为这个博客服务，它也是我的课程平台托管在一个单独的子域上的地方。

## 零停机策略

为了以一种可靠的方式获得零停机时间，你会想改变你的 DNS 记录，以使用你的主机提供商的名称服务器。如果不可能，您也可以使用新注册商的名称服务器，前提是他们允许您在迁移完成前设置 DNS 记录并使用他们的名称服务器。

##### 1。我建议使用您的主机提供商的名称服务器:

例如[数字海洋](https://www.digitalocean.com/?refcode=0a14c0d916b3)提供免费的 DNS 托管和 DNS 记录管理。我在我所有的网站上都使用它们。

一般的想法是，你可以在你决定要转移你的域名前 48 小时，在你的主机服务提供商的网站上为你的域名创建 DNS 记录。这将为世界各地的 DNS 服务器提供充足的时间来赶上。

与此同时，你的旧注册商会有重复的 DNS 记录，但是你会更新你的域名服务器指向你的主机提供商。大约 48 小时后，所有请求将通过您的主机提供商的名称服务器。

现在你处于一个很好的位置，并准备继续转让你的域名。

##### 2。如果不可能，请使用新注册商的域名服务器:

一些域名注册商，如 [Namesilo](https://www.namesilo.com/?rid=4404761du) 对此非常冷静。他们会让你使用他们的域名服务器，并在你的域名转移完成前预先配置你的域名系统记录。我们将在后面详细讨论这个预配置步骤。

如果你的新注册商不提供这种服务，那就考虑使用更好的注册商吧！

此时，您应该执行上述步骤之一。完成后，您就可以继续进行域名转让了。

## 域名转移流程

您需要在新旧注册器上做一些事情。

#### 对旧注册商采取的步骤(第 1 部分)

1.  确保您的 WHOIS 联系信息中的电子邮件地址是最新的。去检查一下，确保你能从你档案上的地址收到邮件。

2.  可选禁用 WHOIS 隐私。一些注册服务商会在域名启用的情况下转移域名，所以请仔细检查。如果您确实启用了 WHOIS 隐私，那么请确保您可以接受来自私人电子邮件地址的电子邮件。

3.  从您注册商的仪表板获取您的 EEP 授权码。这可能是你的域名管理部分的一个选项。这是一串字符。

4.  在您的域管理仪表板中，解锁该域。

#### 对新注册商采取的步骤

1.  在您的新注册商上找到域名转让选项。这是[名称库](https://www.namesilo.com/transfer.php?rid=4404761du)的链接，因为我碰巧在用它们。

2.  输入您要转移的域名，然后在旧注册商提示您输入时输入他们提供的 EEP 授权码。

3.  您应该会在您的 WHOIS 联系人电子邮件地址收到一封电子邮件，您需要点击其中的一个链接来启动迁移。

##### 您的新注册商允许您预先配置任何东西吗？

例如, [Namesilo](https://www.namesilo.com/transfer.php?rid=4404761du) 可以让你在域名转移之前预先配置一些要转移的内容。这是一个非常方便的功能！

在传输 DNS 记录时，他们允许的选项如下:

*   不要更改您的 DNS 记录，在这种情况下，一旦传输完成，它将使用您当前的 DNS 记录。如果你正在使用你的主机提供商的域名服务器，这就是你想要做的。

*   他们将尝试克隆和导入您的旧注册商的 DNS 记录，并为您设置它们，同时也让您可以选择仔细检查和修改一切。如果您使用的是新注册商的域名服务器，您需要进行设置。

*   自动转发(301 重定向)域名到另一个域名。

*   设置域名停放页面。

您还可以启用或禁用 WHOIS 隐私，并编辑您的 WHOIS 联系信息。

当域名转移完成后，所有这些设置将立即生效！

#### 对老注册商采取的步骤(第二部分)

既然你已经开始转移，就看他们是否接受了。你有几个选择。你可以什么都不做，等待，这可能需要几分钟到 7 天的时间，这取决于你的旧注册商的用户友好程度。

##### 您的域名转让结果列表:

*   在绝对最坏的情况下，请求将被拒绝，您的域名转移将失败。你必须联系你以前的登记员，问他们为什么。

*   在第二种最糟糕的情况下，如果您没有任何用户干预，迁移将需要 7 天才能完成，因为您的旧注册服务商忽略了该请求(这是 7 天后自动迁移的默认行为)。

*   在几乎最好的情况下，您可以向他们发送一封支持电子邮件，询问他们是否可以接受迁移，因为其状态目前为*“待注册”*，这意味着它正在等待他们接受您的迁移请求。

*   在最好的情况下，你可以出去几分钟，等你回来的时候就完成了。

当您的旧注册服务商接受迁移时，您将从旧注册服务商、新注册服务商或两个注册服务商处获得迁移确认。这真的取决于你在用谁。

如果你很好奇，当我把这个域名从 internet.bs 移到 Namesilo 时，整个过程花了大约 3 个小时。我采取的方法是联系 internet.bs 的支持人员，让他们知道该域名已经准备好进行迁移，在几封友好的电子邮件后，一切都完成了。

## 大功告成时

您需要仔细检查新注册商的所有信息，以确保您的域名服务器指向正确的位置，您启用了 WHOIS 隐私功能(如果可用),并且您设置了旧注册商的所有电子邮件转发地址。

当涉及到电子邮件转发时，您需要更新您的 DNS 记录，以使用新注册商的 MX 记录，而不是旧注册商的 MX 记录。

你转让你的域名了吗？让我知道下面怎么样了！