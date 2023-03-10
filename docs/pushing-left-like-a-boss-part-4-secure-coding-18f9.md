# 像老板一样向左推:第 4 部分:安全编码

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-4-secure-coding-18f9>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/pushing-left-like-a-boss-part-4-secure-coding/)！

在本系列的前一篇文章中，我们讨论了安全设计的概念，如最小特权、减少攻击面、故障安全和深度防御(分层保护)。在本文中，我们将讨论安全编码原则，这些原则可以用来帮助指导开发人员在软件中实现安全控制。

正如我们之前讨论的，安全缺陷是设计问题，而安全 bug 是实现问题(代码中的问题)。不管是谁写的代码，都是出于好意，但可能没有足够的信息、足够的时间或足够的指导来正确地完成它。

[![Coding Phase of the SDLC](img/0c70237423d8b0fb88f14f0a577c05f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CND6n59a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrmtoy2v8m0mxo7mj7eq.png)

### 什么是“安全编码”？

有时被称为“防御性编码”，这是一种考虑到安全性的编码行为，防止应用程序被意外或有意滥用。它假设您的应用程序将会以各种方式使用(不一定只是您想要的方式),并相应地对其进行编码。

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/pushing-left-like-a-boss-part-4-secure-coding/)