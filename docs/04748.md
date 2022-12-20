# 接受有关当今安全的教育。

> 原文：<https://dev.to/tarialfaro/be-educated-about-today-s-security-1c9a>

*免责声明:安全是一个不断变化的广阔主题。保持更新！今天安全的东西明天可能就不安全了。*

*第二个免责声明:对于这篇文章中的任何错误，我们深表歉意。真的很长，我又不是编辑。*

这种联系将来可能会被打破。

[安全](https://en.wikipedia.org/wiki/Category:Security)有*许多*子类别，这完全取决于具体的环境。这可能是当今最复杂的课题之一。在这篇文章中，我将涉及*很多*主题，但不是全部。安全性是极其广泛的，它不仅仅是密码学。

如果你是一名开发人员，参加一个 [web 应用程序安全测试，](https://timoh6.github.io/WebAppSecQuiz/)并查看一些[加密正确答案。](https://latacora.singles/2018/04/03/cryptographic-right-answers.html)

安全很重要，传播消息，参与，分享你的知识(最好不要粗鲁)。

**我回顾了一些*神话、攻击媒介、最佳实践和资源。*T3】**

## 好吧！让我们回顾一些神话。

是的，流言终结时间。

### “VPN 提供匿名。”(不，他们没有。)

[私人上网](https://www.privateinternetaccess.com/)说:

> 私人互联网接入 VPN 服务加密您的连接，并为您提供一个**匿名 IP** 来保护您的隐私。

[ProtonVPN](https://protonvpn.com/) 说:

> 保持你浏览历史的私密性。作为瑞士 VPN 提供商，我们不会记录用户活动或与第三方共享数据。我们的**匿名 VPN** 服务可以在没有监控的情况下实现互联网。

[NordVPN](https://nordvpn.com/) 说:

> 想象一下 VPN 是一个防黑客攻击的加密隧道，供在线流量流动。没有人能看穿隧道，得到你的互联网数据。每当您在旅途中使用公共 Wi-Fi、访问个人和工作帐户，或者想要保留自己的浏览历史记录时，NordVPN 都会让您高枕无忧。

没有什么是防黑客的。没有人是 100%匿名的。VPN 不提供匿名。VPN 可以看到您的 ISP 所做的一切。问题是，*比起你的 ISP，你更信任你的 VPN 提供商吗？*VPN 可以绕过审查，防止 ISP 看到您通过互联网提出的请求。

当你使用公共 WiFi 时，使用 VPN 来加密你的互联网流量是一个很好的做法。

但是 Tari，VPN 屏蔽了你的 IP！

是的，我知道。但这不是获得匿名的唯一因素。

如果你想匿名，可以去看看 Tor 项目和 T2 I2P 项目。

### “我不需要在类似 UNIX 的机器上杀毒。”(是的，你有。)

仅仅因为你使用的是类似 UNIX 的机器，并不意味着你对病毒免疫。一般来说，病毒对他们来说不太常见，主要是因为许多病毒针对 Windows 和 MacOS 用户。我指的是桌面使用，不是服务器。

这并不意味着反病毒程序是银弹。不要点击那些带有“看看这个 YouTube 视频中的你自己吧！”的网络钓鱼脸书信息。不要。点击。随机的。链接。

查看 [ClamAV](https://clamav.net/) 。

常识很重要！

### “我不重要，黑客不会针对我。”(从经验来看，他们会的。)

通常，许多人在互联网上有许多服务的帐户。Hulu、网飞、银行等。

*银行业*比较敏感。有些人有银行账户，里面有钱。为什么黑客不想要你的钱，或者你的信用卡信息？或者你的亚马逊账户买东西？

真实故事，我妈妈的 Hulu 账户遭到了恶意第三方的攻击。我爸的邮箱和亚马逊账户也被攻破了。是的，这是最近的事，就在几个月前。

### “我必须用我的隐私来换取安全。”(不，你没有。)

隐私是安全的一个类别。加密有助于保护您的隐私。你和你的治疗师之间的谈话应该是保密的，私人的，秘密的。

[机密的定义...](https://www.merriam-webster.com/dictionary/confidential)

密码术可以在某种程度上确保秘密保密(或私人物品保密)。

#### 举例

假设您正在注册一项服务。他们记录你的 IP，用户代理，设备等。它是明文存储的。*不！*他们可以加密，这样 ***只有**你*可以看到认证 IP 和用户代理的列表。他们也可以散列它们，并进行比较。如果不匹配，则发送信息，说明*可能*正在进行可疑的身份验证操作，并要求该会话在尝试登录时提供额外的身份验证因素。

**隐私不是匿名！**

## 转移到攻击载体和漏洞上。

这一部分是面向开发者的。有这么多的攻击媒介。我只会看几个。

SQL 注入；XML 外部实体；会话固定；弱会话标识符；跨站请求伪造；跨站点脚本；反射式跨站点脚本；命令执行；点击顶起；目录遍历；基于文档对象映射的跨站脚本:文件上传漏洞；破坏访问控制；打开重定向；未加密的通信；数据管理不善；

### xx/xee。[多读-我...。](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing)

XML 有一些安全问题。像 XML 外部实体(XXE/XEE)这样的东西是可能的。

### XSS。[阅读更多...](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))

跨站点脚本(XSS)不仅仅是简单的`alert(1)`。*多了。*这是一个复杂的问题，像这样的问题今天仍然在存在。因为 JS、HTML、CSS 有不同的解析器，这意味着它们解析的东西彼此不同。这意味着很难推出安全更新。

这是一个解析器差异问题，有许多不同的方式攻击不同的浏览器。

这篇文章已经很长了。学会减轻 XSS。

### SQLi。[阅读更多...](https://www.owasp.org/index.php/SQL_injection)

SQL 注入(SQLi)用于解决*的大部分问题。这主要是由于遗留项目。然而，漏洞仍然会弹出，但它们通常会被*迅速发现并修补。**

确保使用*准备好的 SQL 语句*来避免这些问题。

### 会话注视。[阅读更多...](https://www.owasp.org/index.php/Session_fixation)

会话标识符最常见的位置通常是在 cookies 中。像 XSS 这样的东西可以提取会话标识符**如果**标志`http_only`被设置为`false`，嗯，据我所知。

如果任何人有这个，他们可以登录到您的帐户。

确保只有 HTTP 可以访问会话标识符 cookies。摧毁 XSS 的弱点。

不要在 JavaScript localStorage 中存储会话标识符！不，除了构建本机桌面和移动应用程序，我没有其他解决方案/替代方案。

### 弱会话标识符。[阅读更多...](https://www.owasp.org/index.php/Session_Prediction)

低熵/容易猜测的会话标识符使得攻击者很容易伪装成经过身份验证的会话。

解决方案是创建一个 256 位的会话标识符。攻击者必须猜测 2^256 的可能性，这将花费太多的时间。**散列这个会话标识符，然后把它存储在客户端。**散列很重要，以防 CSPRNG 出现问题。

#### 储存不当。

不在服务器上散列会话标识符可能会产生问题。如果攻击者破坏了服务器，他们可能被认证为任何个人。在这种情况下，服务器管理员也是攻击者，因为他们可以访问会话标识符。

用一个好的算法散列会话标识符。最好是 SHA2 家族/SHA3 家族/Blake2b。**是的！这里再散列一次。**

### 曲奇篡改。

Cookies 存储在个人设备上。他们完全控制了它。他们可以编辑、删除、制作 cookies 等。他们不可信。

解决方案是使用数字签名来确保 cookie 值不被篡改。如果该值与数字签名不匹配，则报告它，让它尖叫，**并且不使用 COOKIE 值。**

## 最佳实践。

遵循这些最佳实践，并确保向你的家人、朋友、熟人和陌生人推荐这些最佳实践。这一部分是面向普通用户的。

### 保护您的密码。

你应该使用 [diceware passphrases](http://world.std.com/~reinhold/diceware.html) 来保护你的密码管理器、设备登录以及任何不适合使用密码管理器的地方。

### 使用密码管理器。

你可能有很多证书。使用密码管理器来保护您的数据。他们有很多人。坚持使用免费的开源解决方案，因为开发者可以验证应用程序的安全性。

我推荐桌面版的 [KeePassXC](https://keepassxc.org/) ，安卓版的 [KeePassDX](https://keepassdx.com/) 。还有[比特沃顿。](https://bitwarden.com/)

### 尽可能使用多因素认证。

像[硬件安全密钥](https://www.yubico.com/)、TOTP 认证器应用程序、电子邮件和短信 OTP 这样的东西对保护你的账户非常有用。尽可能使用它们。

**注:短信双因素认证不安全，但总比没有双因素认证好。**

### 双重检查链接。

确保你仔细检查链接，不要相信随机的链接。如果它们看起来可疑，不要点击它们。

### 使用杀毒和防火墙软件。

确保在你的系统上使用防病毒软件和防火墙。

* * *

## 保持消息灵通，向好的来源学习。

请注意，这些链接中的一些(咳咳，*很多*)是面向那些了解特定主题的技术细节的人的。我含糊其辞是有原因的，因为主题各不相同。

所有这些我个人已经使用了一段时间。

*   我的网站需要 HTTPS 吗？肯定。
*   [黑客攻击。](https://www.hacksplaining.com/)
*   [拉塔科拉博客。](https://latacora.singles/)
*   [ParagonIE 博客。](https://paragonie.com/blog/)
*   [dev to](https://dev.to/t/security)咄！你来了。
*   [加密堆栈交换。](https://crypto.stackexchange.com/)
*   [安全堆栈交换。](https://security.stackexchange.com/)
*   [websec.io](https://websec.io/)
*   [信息安全手册](https://infosec-handbook.eu/)
*   [电子前沿基金会。](https://eff.org/)
*   [黑客新闻。](https://news.ycombinator.com/)
*   密码工程博客。
*   [施耐尔谈安全。](https://www.schneier.com/)
*   [直播溢出网站。](https://liveoverflow.com/)
*   [OWASP。](https://www.owasp.org/index.php/Main_Page)
*   子 reddits。
    *   [Netsec subreddit。](https://reddit.com/r/netsec/)
    *   [安全子编辑。](https://reddit.com/r/security/)
    *   [隐私隐藏。](https://reddit.com/r/privacy/)
    *   [隐密子编辑。](https://reddit.com/r/crypto/)
    *   [密码学 subreddit。](https://reddit.com/r/cryptography/)
    *   Websec subreddit。
    *   [代码子编辑。对于非标准化的密码。](https://reddit.com/r/codes/)
    *   [Compsec subreddit。](https://reddit.com/r/compsec/)
    *   [斯坦福密码子 reddit。](https://reddit.com/r/stanfordcrypto/)
*   Youtube。
    *   与 Christof Paar 一起介绍密码学。
    *   [压铸。](https://www.youtube.com/channel/UCW6MNdOsqv2E9AjQkv9we7A)
    *   [直播溢出。](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w)
    *   约翰·哈蒙德。
    *   被憎恨的人。
    *   电脑爱好者。

* * *

不要害怕。在下面分享你的想法和资源。