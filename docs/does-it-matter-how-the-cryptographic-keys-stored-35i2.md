# 密钥如何存储很重要吗？

> 原文：<https://dev.to/andrii_fedotov/does-it-matter-how-the-cryptographic-keys-stored-35i2>

私钥存储

安全性只是告诉你加密一切，但不会告诉你如何在特定的软件栈中实现这一点。您必须以受保护的方式存储加密密钥。它可以存储在 HSM、智能卡、加密令牌、服务器硬盘上，甚至写在纸上(分割并存储在保险库中)。该密钥可以是未加密的(明文)，也可以用另一个密钥加密。如果你谈论的加密密钥是你的业务应用程序的一部分，你必须加密存储它们，但是你的软件应该能够在没有人工干预的情况下解密它。

公钥存储

但是公钥呢？数字证书验证失败的几率甚至比用户丢失私钥还要高，因为 CA 是网络罪犯更大、更感兴趣的目标。作为传统信任链系统的替代方案，[区块链](https://github.com/Remmeauth/remme-core)通过设计提供了一个永久的可信时间戳。要破坏这个时间戳需要大量的计算工作——重写整个区块链。这最终使发行者能够轮换他们的发行密钥，而不会破坏第三方可靠地验证交易的能力。区块链是一个分布式分类帐，不依赖于任何可信方，如认证机构。其效果是提高了可用性、独立验证的能力以及避免单点故障的冗余。