# 回顾文章:block chain technology:beyond bit coin

> 原文：<https://dev.to/gustavo94/repasando-articulos-blockchain-technology-beyond-bitcoin-1794>

[克罗斯比(谷歌)、纳齐阿潘(雅虎)、帕塔纳亚克(雅虎)、维尔马(三星研究美国)、卡利亚纳拉曼(飞兆半导体)2016](https://j2-capital.com/wp-content/uploads/2017/11/AIR-2016-Blockchain.pdf)

Blockchain 是近年来由于寻求解决涉及两个用户之间货物或服务交换的多种业务模式的安全、信任和隐私问题而大幅度提高其知名度的技术。目前，这些问题是由一个第三方解决的，该第三方充当中间人，通过帮助有关各方达成共识而获利，但这些第三方并不总是能确保解决安全问题、提供适当程度的隐私，或提供在所有有关方面建立信任所需的透明度。

为了解释 Blockchain 的工作原理，本文主要基于 Bitcoin 项目与 Etherum 的实施，该项目旨在通过使用所谓的“Smart Contracts”来更广泛地解决上述问题，使您能够在满足一组有关各方商定的条件时执行特定的操作。他随后列出了目前正在实施的一些应用程序，分为财务和非财务两部分，最后概述了采用这些技术的一些风险。

*已经对比特币[在这里](https://dev.to/gustavo94/bitcoin-a-peer-to-peer-electronic-cash-system-26b6)的运作方式作了说明，所以我将从本复习中省略*

## 财务应用

*   [*【纳斯达克私募股权】*](https://chain.com/sequence/) :纳斯达克与创业连锁公司. com 共同推出了私募“股票”交易平台，从法律上讲是股权，在上市前阶段，不需要银行介入。

    *   [*【auger】*](https://www.augur.net):这是一个允许提前向可能影响市场的事件出售股票而无需收取高额佣金或依赖集中经纪人的应用程序，另外还通过 Etherum 进行支付。
    *   [*【ever ledger】*](https://www.everledger.io/about-us/about):一家使用块状链跟踪每颗钻石拥有者特征和历史的公司。

## 非财务应用

各种法律效力的证件认证也受到 Blockchain 技术的广泛影响，一些致力于提供这类服务的公司有:

*   [*斯坦普里*T3】](https://stampery.com/)
*   [*【维角】*](https://viacoin.org/)
*   [*街区公证人*](https://www.blocknotary.com/)
*   [*密码公证人*](https://www.ccn.com/crypto-public-notary-uses-bitcoin-block-chain-notarize-digital-content)
*   [*存在证明*](https://proofofexistence.com/)
*   [*归于*](//www.ascribe.io)

文章中提到的其他应用涉及不同的领域，例如，由于流媒体平台增多而面临越来越多的问题的版权，供应链中的伪造控制系统，将多个设备连接到一个独立网络的必要性，以及越来越大的物联网(IoT)网络或区域维护

## 收养风险

*   变革阻力 blockchain 意味着双方之间交易方式的范式转变，这可能会引起主要公司(如 VISA 和 Mastercard)乃至同一用户的抵制，因为它们致力于解决信用卡方面的信任和隐私问题。

*   可扩展性:随着时间的推移，数据块的数量会增加，并且即使使用了诸如 Merkel tree(bit coin)之类的工具，执行单个事务也会变得非常昂贵。

*   数据迁移成本:在上述某些字段中应用 Blockchain 包括冗长的 ETL 进程，如迁移业务中的当前合同和文档(如根所有权)。

*   条例:目前，各国政府对这些 P2P 网络没有任何条例，可能会阻碍其通过。

*   欺诈活动:虽然用户通常担心其隐私或安全问题，但也有另一类人对非法行为保持匿名感兴趣。

*   量子计算:基于数据块链的网络的许多安全性都源于解决密码难题的数学困难，但随着量子计算的到来，当前的算法在硬件层面上可能成本不足以确保所涉及的部分人能够欺骗系统。