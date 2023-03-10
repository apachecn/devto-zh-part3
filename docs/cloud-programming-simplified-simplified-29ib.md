# 简化的云编程:简化

> 原文：<https://dev.to/dabit3/cloud-programming-simplified-simplified-29ib>

[![](img/5ddf6ad5fa627f9c2b444fea8c14f10e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpOKbEud--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w9e259u4c4vegjuc3mik.jpg)

自从“[简化的云编程:关于无服务器计算的 Berkeley 观点](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-3.pdf)”发表已经一周了&我花了一些时间来思考&通读它。

如果你不想读整篇文章，看看这篇文章中我的简明观点。

## 相赠&基本观察

一开始，他们明确区分了**无服务器**和传统方法，他们称之为**服务器云计算**。

他们说"..这两种方法代表了一系列基于功能/以服务器为中心的计算平台的端点”

这句话:

“在云环境中，有服务器的计算就像用低级汇编语言编程，而无服务器的计算就像用 Python 等高级语言编程。”

我从来没有这样想过，但完全有道理&很有说服力。从抽象如何改进的角度来看，这是一个自然的进展。

计算诸如 c = a + b 的简单表达式的汇编语言程序员必须选择一个或多个寄存器来使用，将值加载到那些寄存器中，执行算术运算，然后存储结果。这反映了 serverful 云编程的几个步骤，首先供应资源或确定可用资源，然后加载这些资源以及必要的代码和数据，执行计算，返回或存储结果，并最终管理资源释放。”

"自动内存管理将程序员从管理内存资源中解放出来，而无服务器计算将程序员从管理服务器资源中解放出来."

这让我大开眼界。再一次，这又回到了上一点:*“像用 Python 之类的高级语言编程。”*！

## 定义

另一件事:正如 [@schrockn](https://twitter.com/@schrockn) 在这里[指出的](https://twitter.com/schrockn/status/1094989234648145921)，他们定义无服务器计算的方式真的很有趣。

“虽然打包为 FaaS(功能即服务)产品的云功能代表了无服务器计算的核心，但云平台也提供了专门的无服务器框架，以满足 BaaS(后端即服务)产品的特定应用要求。**简单来说，无服务器计算= FaaS + BaaS"** 。

注意术语“BaaS”在其定义中的明确使用。

## 无服务器与有服务器的成本

云提供商报告称，当将应用迁移到无服务器的[【1】](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-3.pdf)、[【2】](https://www.slideshare.net/TimWagner/serverlessconf-2018-keynote-debunking-serverless-myths)时，客户可以节省 4-10 倍的成本。

“我们预测无服务器应用将会激增。我们还预测，混合云内部应用程序将随着时间的推移而减少，但由于监管约束和数据治理规则，一些部署可能会持续下去。”

同意。

此外，标有*“无服务器计算的吸引力”*部分中的这一行:

"...让云更容易编程有助于吸引新客户，并帮助现有客户更多地利用云产品。”

那句话很关键。

对于不熟悉云的人来说，存在着真正的准入门槛，尤其是前端/移动/客户端开发人员。即使提供了许多现有的框架和抽象，仍然有许多他们完全不熟悉的行话，对我来说就是这样。

**在这个领域有巨大的机会**，即让云更容易访问。这个领域已经有了很大的增长，&我认为在未来几年我们会看到它甚至会加速增长。

这就是为什么我真的很兴奋能够开发像 [Amplify Framework](https://aws-amplify.github.io/) 这样的工具来做这件事。

## 上至预言(我最喜欢的部分)

“我们希望新的 BaaS 存储服务能够扩展适用于无服务器计算的应用类型。这种存储将与本地块存储的性能相匹配，并有短暂和持久的变体”

“我们希望无服务器计算比有服务器计算更容易安全编程，这得益于高级编程抽象和云功能的细粒度隔离”

“安全编程更简单..”。重要的区别。

“我们看不到无服务器计算的成本高于有服务器计算的根本原因，因此我们预测计费模式将会发展，因此几乎任何规模的几乎任何应用程序的成本都不会增加，甚至可能会比无服务器计算低得多”

## 最后还有这个

“无服务器计算将成为云时代的默认计算模式，在很大程度上取代有服务器计算，从而结束客户端-服务器时代。”

还有一些非常有用的信息，关于无服务器的挑战、谬误和陷阱，以及如何解决它们。

探索这个空间真是一个令人兴奋的时刻！

要了解更多我对无服务器的想法以及它如何影响前端开发人员，请查看我的帖子[无服务器计算时代的全栈开发](https://medium.com/@dabit3/full-stack-development-in-the-era-of-serverless-computing-c1e49bba8580)

> 这篇文章是根据我关于这个主题的 tweet 帖子创建的，可以在这里找到。

> ![Nader Dabit profile image](img/e5f4dbee552070447c02ae734d8d58cc.png)Nader Dabit[@ dabi T3](https://dev.to/dabit3)![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)1/19
> 《云编程简化版:关于无服务器计算的 Berkeley 观点》出版已经一周了&我花了一些时间思考&通读它。
> 
> 如果你不想读整篇文章，可以看看我的帖子:
> 
> [www2.eecs.berkeley.edu/Pubs/TechRpts/…](https://t.co/gfjFDUMppK)21:21pm-2019 年 2 月 17 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1097244687365230594)9[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1097244687365230594)19

[https://www2 . eecs . Berkeley . edu/Pubs/techr pts/2019/EECS-2019-3 . pdf](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-3.pdf)。