# DLT 堆栈简介

> 原文：<https://dev.to/radixdlt/introduction-to-the-dlt-stack-2noc>

"控制复杂性是计算机编程的本质."布莱恩·克尼根

创建正确工作的软件是困难的。创建正确工作的分布式、去中心化、无权限、可伸缩的软件……嗯，这是另一个大问题。

为了更容易地思考和交流复杂的软件系统，软件工程师使用了这个被称为“栈”的概念。简单地说，它只是一种将系统的不同部分分开的方式。

有一些众所周知的软件栈，比如用于网络的 OSI 模型或者用于 web 服务的 LAMP 栈。但是就 DLTs/区块链的堆栈而言，似乎还没有一个好的模型，这使得讨论和比较不同的 DLT 解决方案变得很困难。

所以言归正传，下面是我们的 DLT 堆栈建议:
[![alt text](img/2ef156a83631f243ff6dacc24a68caea.png "DLT Stack")](https://res.cloudinary.com/practicaldev/image/fetch/s--bL4eS1o1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-5.png)

它不是完全全面的，也没有描述在 DLT 发生的事情的全部复杂性。但它确实提供了一种简单的语言，在这种语言上，我们可以谈论不同 DLT 的某些方面，并轻松地指向特定的部分。现在让我们定义每一层:

- **应用层**:这一层描述了一个具有高级业务逻辑的应用程序，它可以像钱包一样简单，也可以像“链上”智能契约一样复杂。

- **平台层**:这一层描述了 API 和接口，作为应用程序与“机器”交互的网关。

- **机器层**:该层描述 DLTs 的总账部分。这是物理引擎和结构，当外部参与者添加到分类帐中时，可以对其进行更新。请注意，这不是字面上的硬件机器，而是一个模拟(例如以太坊虚拟机)。

- **共识层**:该层描述了 DLTs 的分布式部分。因为系统是分布式的，所以必须有一种方法来达成共识以解决冲突。对于没有权限的区块链，这样的例子有:PoW，PoS。

使用这种符号，这里有一个基数和以太坊 DLT 模型之间的快速示例比较:

[![alt text](img/a35ecd915ccc763416fdb3c30067281e.png "Stack comparison")](https://res.cloudinary.com/practicaldev/image/fetch/s--dc_beZIn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-4.png)

既然我们对每一层的含义有了很好的了解，我们将在每篇技术文章的开头使用以下图例来说明文章将讨论堆栈的哪一部分:

[![alt text](img/ac856789e3e722a52e588f07f474c47d.png "DLT Stack")](https://res.cloudinary.com/practicaldev/image/fetch/s--_vLBrnXj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.radixdlt.com/wp-content/uploads/2019/05/unnamed-6.png)

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询