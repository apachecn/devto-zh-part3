# 客户端和服务器

> 原文：<https://dev.to/tarialfaro/clients-and-servers-548e>

我认为我们应该更信任客户端而不是服务器来为我们处理数据。

服务器应该尝试更多地关注性能和可用性，而不是个人的安全。这并不意味着完全无视安全！这只是意味着服务器应该专注于保护自己和客户端与其他服务器之间的 T2 通信。

客户应该为个人提供加密的机密性、认证和完整性。客户端本身可以是加密签名的免费开源软件，带有可复制的二进制文件。任何有技术技能的人都可以自己审计源代码。如果是基于社区的，也要有所贡献！

最重要的是，客户端是本地的，而服务器是远程的。这意味着直接攻击客户端比攻击服务器要困难得多，尤其是在个人充分保护自己的情况下。有些可能是容易攻击的目标。这要看情况。如果没有简单的方法将他们的攻击分配给每个人，这使得攻击整个观众变得更加困难。

如果这没有任何意义，就这样想吧。攻击者需要为特定的操作系统创建恶意软件。一些观众可能正在使用 Android、iOS、Linux、MacOS、Windows、FreeBSD 等。这只是一个变量，还有很多。我确信有办法将恶意软件分发给多个操作系统。

因此，这使客户端开发人员能够专注于个人安全，例如自动安全更新、散列密码和在离开设备之前加密机密数据。而服务器更关注存储和组织数据、性能和可用性，而不是个人安全。

不，这并不意味着客户端开发人员应该忽略性能或可用性。但是你应该更信任客户端而不是服务器。

这确实使客户端开发人员成为更大的目标，无论他们的客户端源代码存储在哪里。但这就是为什么他们应该用密码签署他们的软件。

* * *

你对此有什么想法？