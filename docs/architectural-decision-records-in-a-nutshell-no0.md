# 简而言之，架构决策记录

> 原文：<https://dev.to/davidlambauer/architectural-decision-records-in-a-nutshell-no0>

[![Photo by Amelie Ohlrogge on Unsplash](img/7a261b1887ecd2be77eb9685a06538d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2hWGPe_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ndo03z60fqdiqacifeb.jpg)

开发人员知道软件架构是重要的东西。

然而，对于开发新手来说，对于一个人在一个软件的架构中能有多少控制权可能会有一点困惑。这就是架构决策记录的用武之地。

但是什么是 ADR，为什么对开发者来说学习 ADR 如此重要？让我们深入了解你需要知道的关于 ADR 的一切。

## 什么是架构决策记录？

架构决策记录(ADRs)是记录所做的重要架构决策及其背景和后果的文档。ADRs 是软件开发和软件架构中的常见实践。ADR 通常是在普通文档之外使用的，但提供了一种历史记录。

架构决策(AD)本身是一个软件设计选择，它解决了系统中的一个重要需求。架构决策日志(ADL)是为某个项目或组织创建和维护的所有 ADR 的集合。架构上重要的需求(ASR)是对软件系统的架构有可量化影响的特定需求。所有这些标题都在架构知识管理(AKM)的主题中使用。

## 如何以及在哪里使用 ADRs？

在项目中，可以在多个领域使用 ADR，并且可以采取多个步骤来实施 ADR:

### 决定的执行和颁布

广告主要用在软件设计中，因此它们必须传达给资助、开发和操作系统的人并被他们接受。这通常包括赞助商、利益相关者和生产者，但也包括开发团队本身。

需要实现架构上明显的编码风格和代码审查。他们应该关注架构问题和决策。当在软件进化和实现新元素中使软件系统现代化时，也必须考虑 ADs。

### 决策共享

使用 ADR 进行决策共享肯定是可选的，但是许多广告在项目中重复出现。正因为如此，当采用新的和外向的知识管理策略时，项目中过去决策的经验可能是有价值的资产。请记住，群体决策通常是一个活跃的研究课题。

### 决策文档

存在许多用于决策捕获的模板和工具。看看敏捷社区，比如 M. Nygard 的 ADRs。您还可以研究传统的软件工程和架构设计过程，例如 IBM 建议的表格布局。

### 决策指导

有相当多的决策技巧。这些包括通用技术和软件架构特定的技术，比如对话映射。

### 识别决策

问问你自己(或者你的团队):这个项目的架构设计到底有多紧急，有多重要？是必须现在就弄清楚，还是可以等到我们能弄清楚更多的东西的时候？

这就是拥有一支优秀团队的原因。除了公认的设计方法和实践之外，拥有个人和集体经验的混合，可以极大地帮助决策识别。

理想情况下，您应该创建并维护一个与产品待办事项一起工作的决策待办事项。

## 建筑决策记录示例

为了给你一个简单的例子来说明 am ADR 可能是什么样子，我添加了一个例子:

```
# 1\. Use Typescript for all JS related tasks

## Status

Accepted

## Context

The Frontend Team encountered that the Frontend Code Base is growing too fast. Due to that, the code quality suffers. 

## Decision

Introducing a static-typed language as a superset of Javascript shall help to deal with this situation. All JS related tasks will now be written in TypeScript.

## Consequences

All components of the Frontend Workflow have to be refactored to the new built. In addition, all old plain js files should be rewritten in TypeScript. 
```