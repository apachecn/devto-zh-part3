# 以太石灰是什么？第 001 集

> 原文：<https://dev.to/etherlime/what-is-etherlime-episode-001-1jh8>

[https://www.youtube.com/embed/TqVj9ar3y58](https://www.youtube.com/embed/TqVj9ar3y58)

以太石灰由以太坊基金会、以太坊社区基金和 ETHPrize 提供支持。

* * *

以太石灰的故事是必然的。很久以前，当我试图部署我的第一个 mainnet 智能合同系统时，它就开始了。这在 Ropsten 和 Rinkeby 身上都很有效。部署脚本运行完美，我在测试网上的所有手动测试都很成功。当我输入网络直播时，我一点也不知道等待我的是什么

部署在 mainnet 上
这一切都是从正常的事务开始的，突然之间，一切都失败了。由于区块链是一项新技术，我吞下了昂贵的打嗝，并决定重试我的迁移生活。不，又失败了。再一次。再一次。痛苦地哭泣着，我决定用 ethers.js 编写自己的部署 node.js 脚本——这是我当时唯一信任的稳定且不会让我失望的东西。一切都很顺利。

是我还是你？
从那以后，我和许多开发伙伴谈论过他们如何在 mainnet 上部署。每个人都有自己的解决方案——有人使用 remix，有人使用 bash 脚本，有人使用 node.js 脚本。没有人信任这项工作的工具。由于我发现 ethers.js 是这个过程中需要的稳定性，我已经说服了我公司 LimeChain 的一个小团队，开始致力于部署和开发框架——如果不是为了商业用途，只是为了减轻我们自己的痛苦。几个星期后，我们都对结果非常满意，我们决定开源它。

从那时起，我们已经把 Etherlime 发展得不仅仅是部署框架。我们增加了新功能，还众筹了更多功能(见我们的 github 问题[https://github.com/LimeChain/etherlime](https://github.com/LimeChain/etherlime))。它既是一个库，也是一个命令行界面。您可以使用其中一个或两个。

文档是我们深信不疑的东西。如果有什么东西发布了，它会被记录下来，你可以在漂亮的文档中看到。这是唯一的采用方式—[https://ether lime . readthedocs . io](https://etherlime.readthedocs.io)。

下面是 Etherlime 可以做的一些事情的概述(按被添加的时间顺序):视频上也有:[https://www.youtube.com/embed/vQ46B6XPQtI](https://www.youtube.com/embed/vQ46B6XPQtI)

**部署**
显然，最严重的痛点首先得到了解决——通过使用 ethers.js 为部署过程增加了急需的稳定性。部署人员非常罗嗦，告诉您它做的一切，并以一个漂亮闪亮的表格结束。您可以看到已经部署了什么，以及所有执行的事务及其标签(如果您提供了标签的话)。您还可以查看部署的历史记录，只需输入—以太时间历史记录。

**嵌入式 Ganache-cli**
从我们作为区块链开发公司的经验中得到的另一件事是，你需要你的 Ganache-cli 地址的稳定性和大量的假 ETH。所有的开发者都编写了自己的脚本来生成这样一个 ganache，我们决定将它嵌入到 etherlime 中。键入 etherlime ganache 允许您运行这样的 ganache，并在其上非常容易地进行部署

**编译和链接**
接下来自然是将编译器嵌入 etherlime。Etherlime 编译器支持多个编译器版本和优化器，可从命令行启用。没有复杂的配置文件。

单元测试和代码覆盖
最后但同样重要的是，任何区块链项目都应该有覆盖其逻辑的单元测试。这不是一件“拥有就好”的事情——在我们这个不变的世界里，这是必须的。有了以太莱姆，你就可以享用你著名的摩卡咖啡了。我们更进了一步，扩展到包括 assert.revert 这样的东西。Solidity-coverage 库也嵌入在 etherlime 中，所以你可以像检查 etherlime 覆盖率一样简单地检查你的代码覆盖率

以太坊社区基金和以太坊发展以太坊
我们很幸运得到了 ECF 和以太坊的帮助。他们用 90，000 美元帮助团队继续努力使工具变得更好。这笔资金将在未来 6 个月内实现以下目标:

**调试器**
最近由 [https://ethresear.ch](https://ethresear.ch) 进行的一项研究表明，开发社区中有一个共识，即工具最期望的改进是看到更新更好的调试功能。编写调试器不是一件容易的事，但是站在巨人的肩膀上，用我们所有的努力和专业知识，我们相信我们可以创建一个可行的版本，并随着时间的推移逐步改进它。

**代码搭建**
我们反复做的一项主要重复性工作是在客户端应用程序中编写“连接代码”。创建合同实例，将它们与 ABI 和地址连接起来，并通过 Wallets 与它们进行交互。我们希望从 JavaScript 开始，为主要的客户端语言自动生成大量的重复性工作

CLI 单元测试覆盖率
任何 CLI 开发人员最大的恐惧之一，就是对意外发布一个破库版本的不满。我们已经决定，就像智能合同开发一样，我们应该用细致和详尽的单元测试套件来保护我们自己和我们的贡献者。这将为用户和贡献者带来更好的开发体验。

* * *

有用的链接:

*为什么需要以太石灰-[https://medium.com/limechain/etherlim](https://medium.com/limechain/etherlim)...

*电报组-[https://t.me/etherlime/](https://t.me/etherlime/)

*推特-[https://twitter.com/etherlime](https://twitter.com/etherlime)

* Github-[https://github.com/LimeChain/etherlime/](https://github.com/LimeChain/etherlime/)

*文件-[https://etherlime.readthedocs.io/en/l](https://etherlime.readthedocs.io/en/l)...