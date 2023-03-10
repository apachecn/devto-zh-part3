# 在基数分类帐上构建分散式国际象棋

> 原文：<https://dev.to/radixdlt/building-decentralized-chess-on-the-radix-ledger-1371>

[![](img/89108b6fd7d1bf7eef0c6b458cf126dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yP7pD80S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7rown1wtcp0of3a80pm7.jpg)

我们之前写过[游戏](https://www.radixdlt.com/post/introduction-to-non-fungible-tokens-nfts/)如何成为推动全球采用加密资产的第一个领域，不可替代的代币等提供了彻底改变收藏品和游戏相交方式的机会。对于我们的黑客马拉松项目，我们决定将一个超过一千年的游戏移植到 Radix: chess。

# 为什么？

通常，在线下棋需要第三方的集中服务来验证你的棋步，并与另一个玩家同步。通过将国际象棋整合到 Radix 中，它变成了一个实时的、分散的、可审计的、可核实账目的游戏，可以在世界任何地方玩。除了说明即使像国际象棋这样的游戏也可以利用实时 DLT，国际象棋的复杂规则在移植到分散平台时也提出了一个有趣的挑战。

# 如何？

我们决定全力以赴实现我们选择的游戏，并希望用 Radix 实现它的所有规则，这样每个节点都可以充当象棋服务。这包括

-不同的动作取决于被移动的棋子
-确保控制白棋的玩家先走
-在另一个玩家移动
之前不能移动两次-一旦移动，就不能恢复

这些规则需要一定程度的通用性。幸运的是，Radix 是复杂应用逻辑的理想选择，它独特的应用堆栈以[约束机](https://docs.radixdlt.com/alpha/learn/architecture/constraint-machine#docs-internal-guid-047e2002-7fff-03af-0fbc-077c1f63e1f1)的形式提供。

为了实现象棋游戏，我们利用了 Radix 平台的一个关键特性:以粒子的形式创建新的逻辑。考虑到可构建性和可扩展性，应用程序堆栈是从头开始构建的，因此，向系统添加新的粒子是微不足道的。

具体来说，我们添加了两个粒子:一个用于棋盘，一个用于棋步。

当玩家走一步棋时，他们消耗最后一个棋盘粒子，并提交一个新的棋盘状态和他们的棋步(由玩家签名)。然后由分类账检查移动的有效性和真实性，验证

-在给定参考棋盘状态的情况下，移动本身是合法的，
-参考棋盘状态尚未消耗，并且
-声称的下一个棋盘状态是将移动应用于前一个棋盘的结果。

所有这些都是在整个账本中实时完成的，这意味着任何地方的玩家都可以在他们的所有设备上玩，并依靠账本来保持更新。

使用简单的基数库，我们将这些新的粒子与开源 chess GUI 相结合，以提供前端接口。其他一切都通过 Radix 运行。

[![alt text](img/2dde1cd409f2f9a432ce374bd0b74396.png "Chess")](https://res.cloudinary.com/practicaldev/image/fetch/s--IkeyidB8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/pasted-image-0.png)

如果你想理解约束机器 ELI5 的风格，去看看 Josh 的这篇解释者博客吧。[象棋银河护卫队第一卷](https://www.radixdlt.com/post/chess-guardians-of-the-galaxy-vol-1/)

# 那又怎样？

通过在 Radix 上实现这一点，chess 服务免费获得了在其他平台上很难获得的非常好的特性:

-所有参与者之间的免费实时同步(即，可以在多个设备上从任何地方同时玩)
-免费离线游戏(即，如果您正在使用的节点暂时失去连接，您可以继续玩，并在重新连接后优雅地重新加入网络，而不会丢失任何东西)
-免费全局一致性(即，没有棋盘状态的“双重花费”，没有非法移动，没有作弊，没有未经允许移动其他棋子，所有这些都由账本强制执行)
-免费可审计性(即，观众可以观看游戏)

在 Radix 中实现所有这些重要而惊人的特性需要一天的工作。当然，该解决方案还可以进一步完善，但具有所有这些功能的全功能原型在一天内就完成了。

虽然 chess 只是一个玩具示例，但它说明了 Radix 应用程序堆栈的开发速度和多功能性，即使对于复杂的应用程序也是如此。在以后的文章中，我们将会看到更多的例子，并深入了解 Radix 是如何为您工作的。

> 注意:由于 chess 应用程序是为了与内部 betanet 交互而构建的，所以我们只有在公共 betanet 上线后才会对其进行开源。

有问题吗？在 Radix Discord group[(Discord)](https://discord.gg/CZ6WMfW)给我(Florian)或 Alex 发消息，或者你也可以在我们的电报频道(【https://t.me/radix_dlt】)问他们。

订阅我们的邮件列表，以便在我们达到重要里程碑时得到通知—[https://radixdlt.typeform.com/to/nyKvMV?source=DecentraSign](https://radixdlt.typeform.com/to/nyKvMV?source=DecentraSign)