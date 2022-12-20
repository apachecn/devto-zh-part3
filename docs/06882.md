# 如何发送未被标记为垃圾邮件的邮件？

> 原文：<https://dev.to/lyubomir/how-to-send-emails-that-are-not-marked-as-spam-5egj>

最近，我帮助一家公司阻止注册等常见的电子邮件。从用户邮箱中自动标记为垃圾邮件。

当你使用 MailChimp 等第三方服务并使用自己的域名发送邮件时，你必须采取几个步骤来证明所有邮箱你就是你，你不是垃圾邮件发送者。

要做到这一点，所有的服务将有关于如何设置 DKIM 和 SPF 记录到你的域名，你将用来发送电子邮件的指示。通常，我会创建一个类似`mail.example.com`的子域，然后我们必须添加服务提供的那些 DKIM 和 SPF 记录。

它们代表文本，告诉邮箱你使用不同的服务发送邮件，然后你自己的服务器

MailChimp 的例子有:

```
 • DKIM:
    Create a CNAME record for k1._domainkey.yoursite.com with this value: dkim.mcsv.net.

    • SPF:
    Create a TXT record for yoursite.com with this value: v=spf1 include:servers.mcsv.net ?all 
```

如果你按照服务的所有说明，你将有一个带有 DKIM 和 SPF 记录的子域，然后你应该准备好发送所有的简讯和普通电子邮件，没有任何问题。

尽管有上述配置，Gmail 等邮箱可能会将您的电子邮件标记为垃圾邮件。为什么？因为在发送电子邮件时，还有最后一个步骤需要配置。
你的**发自**的记录必须与发件人匹配，否则不可信。

我们举个例子:

```
 Received: by mail.example.com
    From: info@example.com
    To: someuseremail@gmail.com 
```

在该示例中，服务用于发送电子邮件的域不同于在来自的*中使用的域。*

但是如果你把它改成:

```
 Received: by mail.example.com
    From: yourfriends@mail.example.com
    To: someuseremail@gmail.com
    Reply-to: info@example.com 
```

电子邮件不会被标记为垃圾邮件，因为发件人域和来自域的电子邮件*是相同的。当用户想要回复我们的邮件时，他不会写给*[yourfriends@mail.example.com](mailto:yourfriends@mail.example.com)*，而是写给我们在*回复*中定义的内容。*

希望这将有助于他的电子邮件。:)