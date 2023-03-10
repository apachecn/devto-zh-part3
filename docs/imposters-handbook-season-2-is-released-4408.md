# 《骗子手册》第二季发布了

> 原文：<https://dev.to/robconery/imposters-handbook-season-2-is-released-4408>

寒假期间(准确地说是在平安夜)， [Scott Hanselman](https://hanselman.com) 和我发布了 *[冒名顶替者手册](http://bit.ly/2F2vS3Z)* [系列的下一卷](http://bit.ly/2F2vS3Z)[。我们花了 18 个月多一点的时间把它组装起来，我非常满意。](http://bit.ly/2F2vS3Z)

<figure>[![](img/adacdf2e80521b069ca7bcfbff608439.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eVxo_yRw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/01/mutual.png%3Fresize%3D1024%252C768%26ssl%3D1) 

<figcaption>香农第二定理，图经死亡之星</figcaption>

</figure>

## 从逻辑到布尔代数，从二进制到电路

《骗子手册》第一卷的内容太多了，我不得不删掉，因为我的时间和空间都用完了。例如:我对二进制运算知之甚少，对加密更是知之甚少，我非常想改变这一点。不幸的是，它必须等到我有时间。

那是在过去的 18 个月里。斯科特和我深入研究了二进制加减法、逻辑门和布尔代数。

<figure>[![](img/c10cbbac15eb7c42d5ea5e4f5e636348.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wu4nhz9A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rob.conery.io/wp-content/uploads/2019/01/xor.png%3Fresize%3D1024%252C792%26ssl%3D1) 

<figcaption>一些我永远记不住的东西:异或
</figcaption>

</figure>

钻研这些主题很有趣，但我没想到接下来会发生什么。即使现在写这篇文章，我也在努力想出一种方法来准确捕捉一个人工作的独特重要性。他被比作爱因斯坦、爱迪生、牛顿——集三者于一身。

## 克劳德·香农和信息论

克劳德·香农在 1948 年用一篇论文创造了信息论。在这本书里，香农详细描述了一种用数字来描述信息的方法，也就是用 1 和 0。他详细描述了这些信息如何以几乎无损的方式传输，似乎这还不够，他还描述了数字信号可以包含多少信息。

我们今天认为这种事情是理所当然的，但是请记住，在香农的时代，远距离交流的唯一方式是电报或电话！

但是等等，还有呢！

在发明信息论之前，香农写了一篇硕士论文，许多人认为这是有史以来最重要的硕士论文。

## 数字电路的创造

20 世纪 30 年代末，克劳德·香农正在麻省理工学院攻读硕士学位。他还与万尼瓦尔·布什一起研究微分分析器，这是一种房间大小的机械计算机，可以计算微分方程，通常用于军事用途。

<figure>[![](img/7b9e169cfa5b08ffad661fef638334de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XrVaftWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/01/Cambridge_differential_analyser.jpg%3Ffit%3D1024%252C748%26ssl%3D1) 

<figcaption>【双生剑桥大学微分分析仪(公共领域)</figcaption>

</figure>

这台机器必须手动编程，这意味着分解和重建它，在复杂的弹道方程中使用杆、轮和滑轮作为变量。最终，许多机械零件被移动杠杆的电子开关所取代，减少了拆卸和重新组装机器的时间。

这些开关激发了香农的灵感，他回忆起自己在密歇根大学上的一门名为*布尔代数*的课。原来，这个叫乔治·布尔的家伙想出了一个将数学原理应用于简单逻辑命题的方法。香农扩展了这些想法，想出了如何用一系列的*电路*取代整个房间大小的计算机。

**克劳德·香农发明了数字电路**。

## 加密、哈希和区块链

从那时起，故事就开始了。数字电路导致了数字计算机，数字计算机导致了更有效的计算方式，这导致了传输信息的需要，这导致了保密的需要，这导致了我们今天的地位。

斯科特和我深入调查了这一切。

<figure>[![](img/73383b60130c3ca71a76b90f73d5e872.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzTdburl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rob.conery.io/wp-content/uploads/2019/01/rsa_1.png%3Fssl%3D1) 

<figcaption>破解简单的非对称密码</figcaption>

</figure>

我们探索 SSH 密钥和 RSA 如何工作。我们深入哈希，讨论每个哈希的优缺点——包括如何使用彩虹表快速轻松地破解未加盐的信用卡信息。

<figure>[![](img/6235f715d90ee680fd7b3034eac40867.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8k5Wymob--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rob.conery.io/wp-content/uploads/2019/01/sha-gringer.png%3Ffit%3D1024%252C759%26ssl%3D1)

<figcaption>sha 256 的简体涂鸦</figcaption>

</figure>

我们最终以对加密货币和区块链的讨论结束，详细说明了为什么有些人喜欢它，而有些人绝对讨厌它。双方都有一些很好的观点…

写这本书非常有趣——更有趣的是，我和一个朋友一起写了这本书！我真的希望你喜欢它。