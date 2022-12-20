# 揭开 STRIDE 威胁模型的神秘面纱

> 原文：<https://dev.to/pbnj/demystifying-stride-threat-models-230m>

## 目录

*   [简介](#introduction)
*   [什么是威胁模型？](#what-is-a-threat-model)
*   [什么是跨步？](#what-is-stride)
    *   [欺骗](#spoofing)
    *   [篡改](#tampering)
    *   [否认](#repudiation)
    *   [信息披露](#information-disclosure)
    *   [拒绝服务](#denial-of-service)
    *   [特权提升](#elevation-of-privileges)
*   [总结](#summary)
*   [附加资源](#additional-resources)

## 简介

软件正在吞噬世界。因此，软件故障的后果是昂贵的，有时甚至是灾难性的。这在今天的各种事件中都可以看到，从错误配置的 [AWS S3 桶](https://github.com/petermbenjamin/yas3bl)导致的数据泄露事件，到由于[松懈的 API 限制](https://techcrunch.com/2018/06/28/facepalm-2/)导致的脸书数据泄露事件，再到由于使用[带有已知关键漏洞的旧 Apache Struts 版本](https://www.synopsys.com/blogs/software-security/equifax-apache-struts-cve-2017-5638-vulnerability/)导致的 Equifax 事件。

应用程序安全倡导者鼓励开发人员和工程师在软件开发生命周期(SDLC)中尽早采用安全实践 <sup>[1](#tanya-janca)</sup> 。一个这样的安全实践是**威胁建模**。

在本文中，我提供了一种叫做 STRIDE 的方法的高级介绍，在以后的文章中，我将使用一个现有的开源应用程序作为例子来演示这个过程。

## 什么是威胁模型？

以下是必不可少的[维基百科定义](https://en.wikipedia.org/wiki/Threat_model):

> 威胁建模是一个过程，通过该过程可以识别、列举和区分潜在威胁(如结构性漏洞)——所有这些都是从假设的攻击者的角度出发。威胁建模的目的是为防御方提供对可能的攻击者的概况、最可能的攻击媒介以及攻击者最想要的资产的系统分析。

抛开这一点，最简单的英语解释是这样的:

*威胁模型是一种系统化和结构化的方法，用于识别和减轻我们软件中的安全风险*。

做威胁模型有各种各样的方式和方法，其中之一是微软推广的一个过程，叫做 *STRIDE* 。

## 什么是跨步？

STRIDE 是一个首字母缩写词，代表 6 类安全风险:欺骗、篡改、否认、信息泄露、拒绝服务和特权提升。

每一类风险旨在解决安全的一个方面。

让我们深入了解这些类别。

* * *

### 恶搞

欺骗是指冒充他人(即欺骗用户)或声称虚假身份(即欺骗进程)的行为。

这一类与**真实性**有关。

示例:

*   一个用户通过暴力破解用户名/密码凭证来假冒另一个用户的身份。
*   恶意的网络钓鱼主机被建立起来，试图欺骗用户泄露他们的凭证。

您通常可以通过适当的[认证](https://www.owasp.org/index.php/Authentication_Cheat_Sheet)来降低这些风险。

* * *

### 篡改

篡改是指对数据或流程的恶意修改。篡改可能发生在传输中的数据、静态数据或流程上。

这个类别与**完整性**有关。

示例:

*   用户对传输中的数据执行[比特翻转攻击](https://en.wikipedia.org/wiki/Bit-flipping_attack)。
*   用户修改静态/磁盘上的数据。
*   用户对应用程序执行[注入攻击](https://en.wikipedia.org/wiki/Code_injection)。

您通常会通过以下方式降低这些风险:

*   用户输入的正确验证和输出的正确编码。
*   使用准备好的 SQL 语句或存储过程来减少 SQL 注入。
*   与安全静态代码分析工具集成，以识别安全缺陷。
*   整合成分分析工具(如`snyk`、`npm audit`、BlackDuck)...以识别具有已知安全漏洞的第三方库/依赖项。

* * *

### 休妻

否认是指否认一个行为或事件已经发生的能力。

这个类别与**不可否认性**有关。

示例:

*   用户否认执行了破坏性操作(例如，从数据库中删除所有记录)。
*   攻击者通常会删除或截断日志文件，以此来隐藏他们的踪迹。
*   管理员无法确定容器是否开始表现可疑/异常。

您通常可以通过适当的[审计日志](https://www.computerweekly.com/tip/Best-practices-for-audit-log-review-for-IT-security-investigations)来降低这些风险。

* * *

### 信息披露

信息泄露是指数据泄漏或数据泄露。这可能发生在传输中的数据、静态数据，甚至某个流程。

此类别与**保密性**有关。

示例:

*   用户能够窃听、嗅探或读取明文通信。
*   用户能够以明文形式读取磁盘上的数据。
*   用户攻击受 TLS 保护的应用程序，但能够窃取 x.509 (SSL/TLS 证书)解密密钥和其他敏感信息。[是的，这件事发生在](https://en.wikipedia.org/wiki/Heartbleed)。
*   用户能够读取数据库中的敏感数据。

您通常会通过以下方式降低这些风险:

*   实施适当的[加密](https://www.owasp.org/index.php/Cryptographic_Storage_Cheat_Sheet)。
*   避免自签名证书。使用有效的可信证书颁发机构(CA)。

* * *

### 拒绝服务

拒绝服务是指导致服务或网络资源对其目标用户不可用。

这个类别与**可用性**有关。

示例:

*   用户执行 [SYN flood](https://en.wikipedia.org/wiki/SYN_flood) 攻击。
*   存储(即磁盘、驱动器)变得太满。
*   Kubernetes 仪表板暴露在互联网上，允许任何人在您公司的基础设施上部署容器，以挖掘加密货币并使您的合法应用程序缺乏 CPU。是的，那也发生过。

减轻这类安全风险是棘手的，因为解决方案高度依赖于许多因素。

对于 Kubernetes 的例子，您可以使用[资源配额](https://kubernetes.io/docs/concepts/policy/resource-quotas/)来减少资源消耗。

对于存储示例，您可以通过适当的日志轮换和在磁盘接近容量时进行监控/警报来缓解这一问题。

* * *

### 特权的提升

特权提升是指获得不应该拥有的访问权限。

此类别与**授权**有关。

示例:

*   用户利用缓冲区溢出获得系统的根用户权限。
*   对 Kubernetes 没有权限的用户可以通过向带有 Kubernetes API 服务器的 TLS 凭证的容器发送特制的请求来提升他们的权限。是的，这是可能的。

降低这些风险需要做几件事:

*   适当的授权机制(例如基于角色的访问控制)。
*   安全静态代码分析，以确保您的代码几乎没有安全缺陷。
*   成分分析(又名依赖性检查/扫描)，如 [`snyk`](https://snyk.io) 或 [`npm audit`](https://docs.npmjs.com/cli/audit) ，以确保您不依赖已知易受攻击的第三方依赖性。
*   通常实践最小特权原则，比如作为非根用户运行 web 服务器。

* * *

## 总结

因此，STRIDE 是一种威胁模型方法，可以帮助您系统地检查和解决应用程序安全状态中的漏洞。

在以后的文章中，我们将使用一个应用程序来完成这个过程，这样您就可以感受一下它是如何工作的。

如果你想为我的威胁模型提出一个应用程序，请在下面的评论中提出建议。

## 附加资源

*   [https://en . Wikipedia . org/wiki/STRIDE _(安全)](https://en.wikipedia.org/wiki/STRIDE_(security))
*   [https://www . web trends . com/blog/2015/04/threat-modeling-with-stride/](https://www.webtrends.com/blog/2015/04/threat-modeling-with-stride/)
*   [https://www . oreilly . com/library/view/threat-modeling-designing/978118810057/978118810057 c03 . XHTML](https://www.oreilly.com/library/view/threat-modeling-designing/9781118810057/9781118810057c03.xhtml)

* * *

## 脚注

*   :[https://www.youtube.com/watch?v=YGJqpQy79no&wt . MC _ id = shehackspurple-blog-tajan ca](https://www.youtube.com/watch?v=YGJqpQy79no&WT.mc_id=shehackspurple-blog-tajanca)

* * *

## 更新

1.  添加更多针对篡改的缓解措施。
2.  添加更多针对信息泄露的缓解措施。