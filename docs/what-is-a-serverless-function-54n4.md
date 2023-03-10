# 什么是无服务器功能？

> 原文：<https://dev.to/pubnub/what-is-a-serverless-function-54n4>

传统上，无服务器功能是托管在托管基础设施上的单一用途的编程功能。这些通过互联网调用的功能由云计算公司托管和维护。这些公司的工程团队确保无服务器功能具有近乎完美的正常运行时间、遍布全球的冗余实例，并可扩展到任何传入的网络请求量。那么是谁创建了无服务器函数呢？不是云计算公司本身；是他们的顾客。软件开发人员正在将他们的产品代码转移到无服务器功能上。由于更好的代码维护、创纪录的低托管价格以及在托管基础设施上运行它们带来的安心，这很快成为一种流行的模式。除了这些好处，新代码的部署**更快、更简单、更容易自动化**。

## 整体架构与微服务架构

无服务器功能可以考虑[微服务](https://www.pubnub.com/learn/glossary/what-is-a-microservice/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22)。在软件开发社区中，人们对将单一平台分解成多个封装的微服务越来越感兴趣。从历史上看，monoliths 拥有大型的统一代码库，这需要对新代码的任何发布进行整个平台的完全部署。这包括新功能，但也包括单行错误修复。Monoliths 在某些情况下可能是实用的，但是当平台的代码库增长到开发团队很大的阶段时，一些任务就变得很麻烦了。微服务架构提高了代码库的可维护性，并提升了软件开发团队的整体开发体验。微服务使大型工程组织能够将自己分割成松散耦合的自治团队。每个团队可以将注意力集中在一些独立于平台其余部分工作的微服务上。请注意，虽然微服务可以在一定程度上独立维护，但它们仍然是统一团队中的成员。有了微服务架构，新开发人员可以快速加入工程项目，因为他们不需要对整个整体有深刻的理解就可以开始做出实质性的贡献。微服务的另一个优势是，将小代码更新部署到单个服务中，很少或根本不会给客户带来停机时间。当 monolith 需要代码更新时，这可能意味着所有客户在部署期间都会经历停机。

[![monolith compared to microservices](img/649cba929c9a64fb8a07a373480ed458.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2O-tbwOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/04/monolith-microservices.png)

## 功能即服务又名 FaaS

无服务器功能的神奇之处在于自动伸缩和冗余代码部署。这意味着一个应用程序开发者只关心一件事:编写优秀的应用程序代码。云托管提供商将这种产品称为“功能即服务”,简称“FaaS”。对于传统的应用托管，软件开发人员在编写代码时需要反复问自己这些问题:

*   不管每个客户端的物理位置如何，我的服务器能否以非常低的延迟响应所有客户端请求？
*   我是否在代码部署过程中为人为错误创造了空间？
*   我的服务器能处理突然增加的请求量而不超载吗？
*   我的服务器能处理突然增加的请求量而不浪费大量金钱吗？
*   我需要持续监控我的应用基础设施吗？我确实喜欢每晚睡 8 小时。

当软件系统建立在具有无服务器功能的平台上时，这些问题并不适用。无服务器功能代码应该具有完全无状态的逻辑，因此冗余实例不会给客户造成不一致。云托管提供商通常在全球有许多存在点。这意味着运行应用程序的服务器离所有可能的最终用户最近。云托管提供商将同时在世界各地的数据中心冗余地部署无服务器功能。这对开发人员的客户来说是件好事，因为他们的客户端请求会得到尽可能少的响应。所有网络逻辑都由云提供商实施。提供无服务器功能的云托管提供商使用行业标准的最佳实践来实现自动化代码部署。这意味着在部署过程中不会有人为错误中断服务。它允许新代码的快速发布，web 产品几乎没有停机时间。如果有 100 多个托管服务的全球数据中心，自动化会使整个部署近乎即时且更加可靠。无服务器功能托管最有价值的特性之一是开箱即用的自动伸缩。云托管提供商已经让他们客户的闲置服务器成本成为过去。多亏了像 Kubernetes 这样的软件产品，服务可以以自动化的方式有计划地扩展它们的基础设施。无论是扩大规模，还是缩小规模，这种新型的“弹性”基础设施使托管更加高效。这为购买云托管的公司节省了大量成本。云托管提供商有内部工程团队监控无服务器基础设施 **24/7/365** 。这包括在开发者支付的托管费用中。在利用无服务器功能后，凌晨 3 点[的传呼值班](https://www.pagerduty.com/)电话呼叫将成为过去。

## FaaS 供应商

云托管公司正在提供最先进的 FaaS 平台。随着大型科技公司建立越来越多的服务器场，全球消费者的服务器成本正在下降。然而，并非所有的 FaaS 服务都是平等的。FaaS 站台不像电话公司。每个平台都有其独特的闪光之处。

### AWSλ

[AWS Lambda](https://aws.amazon.com/lambda/) 是亚马逊 Web 服务上的无服务器功能环境。支持的编程语言有 Java、Go、PowerShell、Node.js、C#、Python 和 Ruby。在需要按需计算的情况下，使用 Lambda 函数是理想的，例如为文件处理(如文件转换)等操作提供计算。

### Azure 函数

[Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) 是微软 Azure 上的无服务器功能环境。支持的编程语言有 C#、JavaScript、F#、Java、Python 和 TypeScript(trans filed JS)。目前有更多的语言正在 Azure 函数上进行实验。在单一用途的 API 需要为平台填充功能的情况下，使用 Lambda 函数是理想的，例如，为安全存储应用程序数据提供 HTTP 端点。

### 谷歌云功能

[Google Cloud Functions](https://cloud.google.com/functions/) 是 Google 云平台上的无服务器功能环境。支持的编程语言有 JavaScript、Python 和 Go。Google Cloud 功能提供无服务器计算，可以与其他 Google Cloud 服务和客户端应用程序简洁地交互。在数据处理是关键的场景中使用它是理想的——比如从图像和视频中检索相关数据。

### PubNub 函数

[PubNub Functions](https://www.pubnub.com/products/functions/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22) 是一个在边缘执行功能的无服务器环境，在消息通过 PubNub 网络时对其进行转换、丰富和过滤。JavaScript 是唯一受支持的编程语言。这种无服务器平台在执行方式上与其他平台有着根本的不同。当用 [PubNub 的 Pub/Sub API](https://www.pubnub.com/products/realtime-messaging/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22) 发布消息时，执行 PubNub 函数的无服务器事件处理程序。开发人员可以在传输中的 PubNub 消息发布后，但在该消息到达订阅者之前对其执行代码。还有其他执行配置，包括发布后非阻塞，以及可以像 Node.js 服务器一样访问的 REST API 端点。可以在函数环境中使用内置的 [KV-Store](https://www.pubnub.com/docs/blocks/kvstore-module?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22) 来管理状态。在 [PubNub Blocks 目录](https://www.pubnub.com/docs/blocks-catalog?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22)上还有一个开源的第三方集成目录。

## 我应该使用哪个 FaaS 提供商的服务？

这个问题的答案取决于需要构建的服务的类型。正如您从上面描述的提供者中看到的，根据云的不同，有不同的编程语言可供使用。选择提供商时需要考虑的最重要的事情是:**哪些属性对我的服务至关重要？**最大的云公司(AWS、Azure、Google)提供的解决方案旨在通过通用云产品适应几乎所有情况。像 PubNub 这样的公司专注于解决开发人员痛点的专业化，因此 PubNub 在实时用例中完全胜过一般的无服务器产品。PubNub 函数使传输中的消息计算表现出极低的执行延迟。此外，PubNub 函数易于构建和部署。PubNub 功能非常适合实时应用，如聊天、GPS 位置跟踪、物联网信令、多人游戏等。如果你的需求是大量的计算，大量的需要操作系统访问的语言，并且不是实时关键的，你最好使用任何大型云托管公司。参见 PubNub 博客上的[对 PubNub 函数和 AWS Lambda 的比较，了解更多关于选择无服务器提供商的观点。](https://www.pubnub.com/blog/comparing-pubnub-functions-vs-aws-lambda-functions/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-May-22)