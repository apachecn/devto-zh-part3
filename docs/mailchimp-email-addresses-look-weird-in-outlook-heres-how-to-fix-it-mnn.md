# MailChimp 电子邮件地址在 Outlook 中看起来很奇怪？以下是修复方法。

> 原文：<https://dev.to/tvanantwerp/mailchimp-email-addresses-look-weird-in-outlook-heres-how-to-fix-it-mnn>

*[原文发表于 2016 年 9 月 22 日媒体上。](https://medium.com/@tvanantwerp/mailchimp-email-addresses-look-weird-in-outlook-heres-how-to-fix-it-bcafd99f8497)T3】*

## 问题

我收到许多使用 MailChimp 的组织发来的电子邮件。我怎么知道他们用的是 MailChimp？因为他们的邮件总是这样开头:

```
Sender <person=example.com@mail215.atl61.mcsv.net> on behalf of Sender <person@example.com> 
```

我在工作中使用 Office 365，通常在 Outlook 中浏览电子邮件。我收到的每一封 MailChimp 邮件都有一个和上面一样的乱码发件人地址。

因为电子邮件是由 MailChimp 的服务器发送的，所以发件人地址通常包括该 MailChimp 服务器的域。这导致了三个问题:

1.  太丑了。
2.  我可能会认为这是垃圾邮件。
3.  我从来不会把来自你的域名的邮件列入白名单，因为它从来都不是你的域名——它总是一个不同的 MailChimp 服务器！

这很容易解决。你所需要做的就是对你的域名做一些 DNS 调整，你的邮件将不会在 Outlook 的“发件人”栏显示这些混乱的信息。

## 修罗

你需要做的就是添加两个 DNS 记录。

```
Type:  CNAME
Host:  k1._domainkey.example.com
Value: dkim.mcsv.net

Type:  TXT
Host:  example.com
Value: v=spf1 include:servers.mcsv.net ?all 
```

就是这样！一旦您添加/修改了这些 DNS 记录，您从 MailChimp 发送到 Outlook 客户端的电子邮件地址看起来就没问题了！

您可能已经有一个以`v=spf1`开始的 TXT 记录，在这种情况下，您只需要将`include:servers.mcsv.net`添加到该记录中已经存在的域列表中。

## 解释

当 Outlook 首先显示垃圾发件人地址时，是因为它不知道是否应该信任 MailChimp 服务器作为您的域的合法发件人。 [DKIM](https://support.google.com/a/answer/174124?hl=en) 和 [SPF](https://support.google.com/a/answer/33786?hl=en) 记录旨在帮助电子邮件服务器知道谁可以或不可以合法地代表一个域名发送邮件。有了这些记录，电子邮件将正确显示为`Sender <person@example.com>`，因为 Outlook 有办法验证 MailChimp 是否被授权为`example.com`域发送邮件。