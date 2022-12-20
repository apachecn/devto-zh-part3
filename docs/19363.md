# 完成的定义

> 原文：<https://dev.to/brightdevs/definition-of-done-41h6>

我经常听说某样东西“差不多准备好了”。这个术语含糊不清，带来的困惑多于信息。根据[90 法则](https://brightinventions.pl/blog/dont-be-mockup-developer/#ninety-ninety-rule)，90%的代码在 90%的项目时间内实现，而剩下的 10%的代码(“几乎就绪的特性”)可能会花费同样多的时间，这将导致大量的项目延迟。

未完成的工作有增加的趋势，如果看不到真正还剩下多少工作，赤字会很快失控。因此，确定完成的定义对每个项目都至关重要。否则，你只能哼着悲伤的项目经理歌:

> 做了什么？
> 
> Devs 不要伤害我
> 
> 不要伤害我
> 
> 不再

## 没有单一的定义

完成(DoD)的定义是为产品增加可验证/可证明价值的特性和活动的列表。它也可以被定义为为了完成一个特征而必须完成的步骤的列表。验证 Done 的定义将确保您交付的特性是真正完成的，不仅在功能方面，而且在质量方面。

准备一个适合所有情况的国防部是不可能的。每个团队都应该合作，提出适合其独特环境的定义。首先，您应该定义需要交付给最终客户的工件(应用程序、文档、发行说明等)。).然后，考虑当前的环境和能力，您应该决定在每个 Sprint 中可以完成哪些活动。根据具体情况，有些标准可能不是强制性的(如考虑分析或国际化)。对于一个 Scrum 纯粹主义者来说，这样的可选标准不应该包含在 Done 的定义中。在我看来，它们帮助团队记住在某些情况下必须满足的需求。

[![Dictionary image](img/ba4bcc80390aa86c97fd1613daa95fa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bjm3EMXL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z1n2c5augev4eq6bueio.jpg)

完成的定义不仅适用于特写/故事。根据 Scrum Alliance 的说法，团队在不同级别可能有不同的 DoD:

> *   Completion definition of features (story or product to-do)
>     
>     
> *   Done definition of sprint (set of features developed in Sprint)
>     
>     
> *   "Completion" definition (potential shippable status) of distribution

## 特征阶段

在我运行的项目中，功能/故事被分解成各个阶段，每个阶段都有标准。为了满足“完成”的定义，必须完成所有阶段并满足所有标准。交付符合行业标准的高质量产品至关重要。描述特征阶段可以确保团队中的每个人都确切地知道团队交付的每样东西的预期。它还允许我验证该特性实际上处于哪个阶段，如果它在某个阶段停留的时间太长，这就是可能有问题的信号。

我使用的示例特征阶段:

1.  打开
    *   等待分配
2.  在发展中
    *   已分配，在制品(WIP)
3.  暂停*
    *   可选，以防工作无法继续
    *   需要采取的行动(AR)
4.  已实施/准备审核
    *   根据标准编写的代码
    *   没有 linter 错误，没有编译器警告(除非有理由)
    *   单元测试通过
    *   记录的变更*
    *   i18n 已考虑*
    *   考虑的分析*
    *   考虑安全性和数据保护*
    *   推送到远程存储库的代码
    *   在 CI 服务器上自动构建
    *   从 CI 服务器部署到临时环境的应用程序
    *   准备接受同行评审(特性测试+代码评审)
5.  已部署/准备好进行质量保证
    *   通过同行评审
    *   准备接受测试
6.  **完成**
    *   通过验收测试
    *   可以立即部署到生产环境中

请记住，功能阶段可能取决于许多因素，如同行评审过程、应用环境、部署过程、验收测试以及您与客户合作的方式。它们从来都不是固定的，应该为每个项目单独确定。

## 双赢

[![Happy image](img/f36fcf6ac7c389ef4e6408c4e6590f6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ze90BWoj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a91dl5jwfzlzgcy7nhfz.jpg)

清晰的工作流程和明确的 Done 定义是团队顺利工作的必要条件。当项目经理充分了解进度和可能的延迟时，他可以很好地与风险承担者沟通。如果遇到任何障碍，他可以迅速做出反应，促进发展进程。另一方面，开发人员没有长时间工作的压力感，因为它被划分为清晰的阶段。完成的定义确保了您正在交付的特性是真正以适当的质量水平完成的。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Mateusz Klimczak,《推动事物前进》@光明的发明

[推特](https://twitter.com/Klimczak_M)，[邮件](mateusz.klimczak@brightinventions.pl)