# 微服务

> 原文：<https://dev.to/rafaelmonteiro/microservices-1lgf>

## 简介

自从 SaaS 应用程序的爆炸式增长以来，在 API 领域出现了许多创新
,提供了应用程序
功能之间的重要交互。

围绕微服务的主要思想是，一般来说，当应用被视为模块时，它们会变得更容易开发。考虑到这一点，
每一部分都是单独开发的，是所有
部件共同工作的应用组合。它反对传统的开发，传统的开发中
应用程序被认为是一个“单体”实体。

这样，开发人员可以专注于他们自己的核心业务，而其他专家提供所需的组件，这些组件将通过 API 访问。

<figure>

[![Monolithic vs Microservice](img/76029a8c2f44821a1ebb53719c195fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PXYIczfA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dzone.com/storage/temp/570495-slide1.png)

<figcaption>Monolithic vs Microservice</figcaption>

</figure>

## 工作原理

一般来说，微服务通常(但不是唯一地)通过无状态 REST API 提供 API 端点，该 API 可以作为传统网页通过 HTTP 访问。因此，消费这些服务很容易，因为它们只需要开发人员已经熟悉的
工具和方法。

## 好处

当应用组件被拆分时，开发时间得到优化，因为它们是同时产生的。除此之外，在
mind 中用这种思想创建的模块更容易在其他环境/应用中重用。

弹性似乎是一个额外的优势，因为组件可以分散到多个服务器甚至各种数据中心。在一个组件出现故障的情况下，
您可以快速替换它，而应用程序的其余部分可以像
预期的那样继续工作。

扩展也变得更加高效，因为您可以专注于最苛刻的部分，而不是简单地复制整个应用程序。

## 例子

如下图所示，在不同的区域有几个玩家:

<figure>

[![Microservices players](img/6f12eee1cb47a8aed9d84db6456d3b23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RYm2tfzI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://rafaelmonteiro.github.img/microservices_players.jpg)

<figcaption>[Microservices players (image by Menlo Ventures)](http://rafaelmonteiro.github.io/ "Microservices players").</figcaption>

</figure>

## 结论

微服务要求 IT 部门朝着 DevOps
文化进行概念上的转变，在 devo PS
文化中，开发和运营团队通力合作，在整个生命周期中支持
应用，并经历快速甚至连续的
发布周期，而不是传统的漫长周期。

尽管微服务不是灵丹妙药，但在设计新应用或重构旧应用时，必须考虑微服务。