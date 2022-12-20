# 2019 年学习 CF 的五大理由

> 原文：<https://dev.to/mikeborn/five-reasons-to-learn-cf-in-2019-2odc>

我很高兴提出 2019 年学习 CF 的五个理由。我在这里试着诚实，但是你可能会发现一些偏见，因为我喜欢 ColdFusion。在那里，我坦白了。请继续阅读！

## 学习 CFML 的五大理由

1.  太好写了！！
2.  超级强大- ldap，数据库，imap，pop，ftp 和缓存支持内置
3.  强大的安全性——从 ESAPI 支持到 Pete Frietag 这样的安全专家。
4.  巨大的就业市场，可以获得“独角兽”工资
5.  社区非常紧密，软件包维护者提供了很多帮助

## CF 就是这么好写

如果你只从这篇文章中拿走一句话，那就是:你可以用 Coldfusion 比任何其他语言更快地(并且缺陷更少地)构建应用程序。你根本找不到更简单的编程语言。(请不要回复“Python”，因为我会马上回击:“空白！”)Coldfusion 是围绕[快速应用程序开发](https://airbrake.io/blog/sdlc/rapid-application-development)方法设计的，应该很适合任何寻求快速迭代开发的团队。(敏捷，有人吗？)

但是为了避免有人认为我在虚张声势，我认为 CF 很容易编写的原因如下:

### CF 为什么这么好写？

1.  CF 有两种熟悉的语法——标签和脚本——这两种语法对于前端开发人员来说应该很容易掌握。如今，大多数现代 CFML 倾向于脚本语法。
2.  没有胡扯的空白要求。抱歉，Python——这让你的代码看起来很漂亮，但对于新开发人员来说肯定更难掌握。
3.  内置特性，而不是文档不完善、设计不一致的第三方库。我不想贬低第三方库和框架，但是我认为你绝对不需要安装一个库来执行基本的语言功能(比如文件操作、电子邮件、HTTP 调用等等)。)

## CF 超级强大

CF 是如此的有用和功能丰富，以至于在 CFML 编程八年后，我仍然会遇到我从来不知道存在的功能。有很多令人惊叹的事情？默认内置 -type 功能，你可以想象出专注于以下任何一项的创业公司或小型网络应用:

*   构建 CRUD 应用程序
*   提供电子邮件通讯
*   将电子表格导入数据库
*   写一个博客引擎
*   将 web 表单发送到可填写的 PDF

接下来，我将摘录我的 [Why CF？](https://michaelborn.me/entry/why-cf)博客帖子，这只是 CF 中如此简单而在其他语言中如此困难的功能的一个例子:

> 几年前，我用 PHP 和 Bolt CMS 为一个画家建了一个网站。客户的另一个要求是在网站上提供的每张图片中嵌入水印。尽管我尽了最大努力，但我无法在客户的最低预算内完成这项工作——如果没有像`GD`这样的附加扩展，PHP 的图像处理功能就有些微不足道了。
> 
> 在 CF 中，有一个函数允许这样做:`imagePaste()`。取一个水印图像并粘贴到第二个图像的左上角。将图像写入文件。完成了。
> 
> [MichaelBorn.me/entry/why-cf](https://michaelborn.me/entry/why-cf)

## CF 超级安全

CF 为什么超级安全？

1.  通过 OWASP ESAPI 内置 XSS 净化库
2.  用于身份验证、哈希和加密的内置安全功能
3.  有安全意识的社区和行业

由于内置了 [OWASP ESAPI 库](https://www.owasp.org/index.php/Category:OWASP_Enterprise_Security_API)，ColdFusion 在 XSS 漏洞方面非常安全。ESAPI 库提供了奇妙的 XSS 安全规范化函数，如`encodeForHTMLAttribute()`或简单的`canonicalize()`。

CFML 安全性高的第二个原因是内置认证功能过多。你可以用一个[单函数调用](https://cfdocs.org/hash)，[加密用户的电子邮件地址](https://cfdocs.org/encrypt)，或者用`cflogin()`和`isUserInRole()`构建强大的基于角色的认证，在`SHA-512`算法中对密码进行 10000 次迭代。

最后，请记住，许多 CF 应用程序是在政府、卫生或银行行业。仅这一事实就证明了 CF 的安全实力*和*推动了 CFML 的安全努力。这就是为什么我们有像[皮特·弗里泰格](https://www.petefreitag.com)和[查理·阿雷哈特](https://www.carehart.org)这样专注于 Coldfusion 安全产品的聪明人。您可以遵循 Pete、Charlie 或 Adobe 团队的锁定指南，购买一个 [CF 专用防火墙](https://foundeo.com/security/fuseguard/)，或者使用 [Fixinator.app](https://fixinator.app) 在 CI 构建中执行安全扫描，以限制发布到生产中的安全缺陷数量。

## 巨大的 CF 就业市场

让我们面对现实吧:如果你能在 ColdFusion 中很好地编程，你就是童话中的独角兽。有很多 CF 的工作，很少有开发人员去做。因此，一个好的 ColdFusion 开发人员享有良好的前景和高薪。([认真地](https://www.ziprecruiter.com/Salaries/Coldfusion-Salary))。)最近有人在 Twitter 上告诉我，如果定位正确，我可以在 Coldfusion consulting 拿到“独角兽工资”。在 Monster 上快速搜索工作，确实在当地的一所医科大学找到了许多不错的机会。CF 的 JVM 特性意味着，如果你真的想离开这个生态系统，你可以很容易地使用类似的 JVM 语言或 Java。

## CF 社区

坦率地说，CF 社区很棒，这一切都源于 CFML·斯莱克集团。有语法问题吗？问 Slack。努力开始使用 Coldbox 框架？问 Slack。通常关于 ORM 使用的问题都是由 cold fusion ORM 一书的作者 [John Wish 来回答的。我自己最近问了一个关于](https://www.coldfusionormbook.com/) [CommandBox CLI 工具](https://www.ortussolutions.com/products/commandbox)的问题，不仅收到了来自 [Brad Wood](http://wwvv.codersrevolution.com/) 的回答，而且他还在 48 小时内更新了 CommandBox *以防止将来再出现此类问题。对于一个开源项目来说，这是非常好的客户服务。；)*

## 结论

我相信 ColdFusion 是最好的编程语言之一，而且今天也值得学习。不同意？我的[推特 DMs 开启](https://twitter.com/michaelborn_me)！同意吗？分享这个帖子。:)