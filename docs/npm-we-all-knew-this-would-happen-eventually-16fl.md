# NPM...我们都知道这最终会发生

> 原文：<https://dev.to/m4rk9696/npm-we-all-knew-this-would-happen-eventually-16fl>

我记得当这篇文章第一次出现在媒体上时，我读过它

这篇文章的一些亮点

> 以下是一个真实的故事。也可能只是基于一个真实的故事。也许这根本不是真的。
> 
> 虽然这都是瞎编的，但让我担心的是这些都不难。
> 
> 那里不缺少聪明、讨厌的人，还有 580，000 个 npm 包。在我看来，这些包中至少有一个含有恶意代码的可能性更大，如果做得好，你甚至永远也不会知道。

日期是 2018 年 1 月 6 日，九个月后我们有了这个
[我不知道该说什么。](https://github.com/dominictarr/event-stream/issues/116)

这一切都让我想起了[墨菲定律](https://en.wikipedia.org/wiki/Murphy%27s_law)。

我知道已经有一个[帖子](https://dev.to/nepeckman/how-do-we-improve-security-in-the-npm-ecosystem-3hmj)在讨论如何提高安全性，但我最担心的是**如何毒害**Github 线程。在这里引用他们会让我被禁的。

我知道这是一个非常非常严肃的问题，但是让我们假设你刚接触开源或者编码，你最终成功地做了一个对社区有用的项目。

有一天，你打开你的知识库，发现大约 500 条评论，人们大声指责你是如何把事情搞砸的，而你却不知道为什么。那不是最好的社区体验。

我一直认为麻省理工学院的许可证提到这是可能发生的

> 该软件按“原样”提供，不提供任何形式的担保

我个人是用[电子](https://github.com/electron/electron)之前从 [tux0r](https://dev.to/tux0r) 这个社区指出[这个](https://www.trustwave.com/Resources/SpiderLabs-Blog/CVE-2018-1000136---Electron-nodeIntegration-Bypass/)

> 这使得 XSS 特别危险，因为攻击者的有效负载可以允许做一些令人讨厌的事情，如在 child_process 模块中执行 require 和在客户端执行系统命令

[链接到实际评论](https://dev.to/tux0r/comment/6gh3)，小呼一声谢谢

大人物确实会搞砸

*   [OpenSSL](https://en.wikipedia.org/wiki/OpenSSL#Notable_vulnerabilities) 的缩写形式
*   频谱
*   [熔化](https://en.wikipedia.org/wiki/Meltdown_(security_vulnerability))

我想到的是唐纳德·克努特的这句话

> 犯错
> 和犯错
> 和再次犯错
> 但是越来越少
> 和越来越少