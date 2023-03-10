# 基数积木-文件公证(现场)

> 原文：<https://dev.to/radixdlt/radix-building-blocks-file-notarization-live-c1a>

# 简介

由于公共 DLT 的存储不变性和时间戳功能，公证是交易服务之外最常引用的用例之一。本文将描述如何使用基数分类帐创建一个基本的公证流程:

1.爱丽丝上传文件
2。文件得到公证
3。爱丽丝把文件发给鲍勃
4。鲍勃上传文件
5。Bob 证实 Alice 是对文件进行公证的人

> 注意:在本例中，我们使用了一种不适合大规模部署的方法，因为它还会减少可用的[密钥空间](https://en.wikipedia.org/wiki/Key_space_(cryptography))，并增加密钥冲突的可能性。这对于用例来说不是直接问题，但如果用于数十亿个文件，则会给网络带来更大的问题。

# 工作原理

这里我们利用了这样一个事实，即文件可以被[散列](https://www.radixdlt.com/post/primer-on-hashes-and-hash-functions)成一个[种子](https://en.wikipedia.org/wiki/Random_seed)，然后这个种子可以用来生成一个[私有/公共密钥对](https://www.radixdlt.com/post/primer-on-public-key-cryptography)，它映射到 Radix 分类账上的一个地址。这个简单的函数意味着相同的文件将总是生成相同的密钥对；允许任何拥有该文件的人也能找到公共地址。

Radix 中的所有事务都被打上了时间戳，既通过[多个逻辑时钟](https://docs.radixdlt.com/alpha/learn/platform/tempo#creating-relative-order-of-events-on-the-radix-ledger)，也通过[挂钟时间戳](https://papers.radixdlt.com/tempo/latest/#temporal-proof-provisioning)。在 Alpha 网络上，这个挂钟时间没有经过共识验证，但是在 Beta 网络上，这意味着时间戳可以被信任到与事务共识几乎相同的水平。

因此，数据事务允许在分类账上[存储信息，任何请求它的客户端](https://docs.radixdlt.com/alpha/developer/javascript-client-library-guide/code-examples#storing-an-application-payload)都可以轻松地[读取这些信息，并以人类可读的方式为这些事务打上时间戳。](https://docs.radixdlt.com/alpha/developer/javascript-client-library-guide/code-examples#reading-atoms-from-a-public-address)

# 设置

下面的步骤使用公开可用的 Radix 工具，并在其上构建一个简单的 web 应用程序。

# 第 0 步

爱丽丝有一份文件，她想公证。她打开一个 web 应用程序，并将文件上传到其中。

# 第一步

web 应用程序[对文件](https://www.radixdlt.com/post/primer-on-hashes-and-hash-functions)进行哈希处理，并使用该哈希作为基数分类账上文件的[账户](https://docs.radixdlt.com/alpha/developer/javascript-client-library-guide/quick-start#account) / [身份](https://docs.radixdlt.com/alpha/developer/javascript-client-library-guide/quick-start#identity)的确定性[种子](https://en.wikipedia.org/wiki/Random_seed)。然后，web 应用程序[查询分类账](https://docs.radixdlt.com/alpha/developer/javascript-client-library-guide/code-examples#reading-atoms-from-a-public-address)上的账户地址，以查看该账户上是否有“认领”消息。

# 第二步

如果没有找到“认领”消息，系统会向该帐户发送自己的“认领”消息。

“声明”的消息可以是爱丽丝希望的任何内容，可以使用帐户的公钥加密，也可以是不加密的消息。如果使用帐户的公钥加密，任何拥有原始文件的人以后都可以解密，但没有原始文件的人不能。

“认领”消息还可以包括 Alice 的公钥，以便她可以在以后证明她是认领该账户的人。

# 第三步

Alice 与 Bob 共享该文件(以及可选的公钥和签名)。他也执行了步骤 0 和 1，但是 web 应用程序现在将返回 Alice 的“声明”消息，以及原始交易的时间戳。如果 Bob 还提供了 Alice 的公钥和签名，那么很容易验证 Alice 肯定是声明者。

# 分机

这个例子可以扩展为 Bob 也向文档帐户发送一个“声明的”消息，包括他的公钥。现在 Bob 和 Alice 都可以证明他们有原始文件的副本，以及这些副本在什么时间/日期进行了公证。

这还可以通过 Bob 和 Alice 使用他们的 Radix 身份从他们自己的帐户发送签名消息来进一步扩展，并使用文档的帐户公钥进行加密。现在他们还可以做一个简单的法律合同协议，与公证文件挂钩。

# 结论

虽然这是基数上最基本的使用情形之一，只使用种子帐户和消息，但它清楚地表明，在基数上创建这样的 dApps 要容易得多，只需使用客户端逻辑和 API，而不是复杂而昂贵的智能合约。

在未来的迭代中，我们将看到如何使用帐户所有权、多签名帐户和其他工具来改进约束，从而允许所有权转移和第三方接受等功能。

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询