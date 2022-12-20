# 将整体 SaaS 应用程序迁移到无服务器—决策日志

> 原文：<https://dev.to/paulswail/migrating-a-monolithic-saas-app-to-serverless--a-decision-journal-3a02>

如果你读过我的任何一篇文章，你可能会知道我是一个在无服务器平台上构建绿地应用的拥护者。有无数的[教程和案例研究](https://winterwindsoftware.com/real-world-serverless-case-studies/)的人已经做到了这一点，但没有多少关于遗留生产应用(有真正的付费用户)向无服务器迁移的故事，以及这些带来的所有挑战。

为此，我决定公开记录我在 AWS 上将生产应用从基于服务器的架构迁移到无服务器架构的过程。

以下是我希望你能从这个系列中得到的:

*   关于将基于服务器的整体系统转换为无服务器微服务架构的真实描述。
*   更深入地了解 AWS 无服务器套件中的关键服务及其优缺点。
*   提问或就解决特定问题的方法提出建议的机会。
*   洞察我的决策推理，我的 SaaS 业务和一些数字。
*   帮助我理解或实施部分迁移的资源链接。
*   大量代码示例。

## 被迁移的应用是什么？

首先，一些个人背景。尽管我的主要工作是作为一名全栈开发人员/顾问，我也已经运行我自己的 SaaS 产品超过 5 年了。 [Autochart](https://autochart.io) 是一款面向汽车经销商网站的网站访客分析/销售线索管理应用。它的客户一直在慢慢增长，现在已经有数百名用户，并为我提供了很大一部分收入。

Autochart 已经经历了几年的架构迭代，在过去的 18 个月里，我已经引入了一些无服务器的微服务(使用 API Gateway、Lambda 和 Kinesis)。然而，在这些微服务的下游，系统的核心仍然是一个 web 门户/API，作为运行在 AWS ECS 中的容器上的 monolithic Express.js 应用程序而构建，带有一个 [mLab](https://mlab.com/) MongoDB 数据库。

## 我这样做有什么商业利益？

将一个稳定的生产系统迁移到一个新的架构不是一件轻而易举的事情。尤其是迁移到无服务器几乎肯定会涉及到对现有代码库的大量重写，不像，比方说，如果您正在迁移一个传统的基于服务器的应用程序来运行在容器内，其中的更改通常仅限于基础设施级别。在继续之前，你应该做你自己的成本效益分析。

我这样做的主要动机是:

*   降低成本和资源(mLab 是很好的服务，但是很贵)。
*   能够以较小的风险独立部署新功能。当前的代码库已经积累了一些技术债务，这使得在某些领域做出改变变得困难。
*   当客户网站流量激增时，减少/消除间歇性错误。
*   通过减少大量第三方库的使用来减少安全责任(这是 ExpressJS monolith 所需要的)。
*   停止花费时间修补 EC2 实例。
*   为了更容易从 N.Virginia AWS 地区迁移到爱尔兰，因为它更接近我的客户群，也减少了我的 GDPR 责任。
*   为了在 AWS 无服务器技术方面积累更深层次的实际专业知识，我可以将其应用到我或我的客户的未来产品中。

## 我受到什么约束？

在接下来的几个月里，我将每周花几个小时进行这种迁移。我没有硬性的期限来满足。但是有一些限制:

*   对客户来说必须是无缝的:没有停机时间或对服务的损害。我关心我的顾客，他们为我提供收入，我不想辜负他们的信任和支持。这是我自己的事，所以我需要保持低风险。
*   成批迁移，尽可能避免大规模的切换。这意味着我担心东西坏掉的压力减少了。它还允许我在工作中积累小的成功，如果有更重要的事情发生(或者如果我完全改变了主意，认为收益超过了我的时间投入)，我可以很容易地暂停工作。
*   REST API 契约不能改变，因为它被前端 SPA(我不想碰它)和一些来自第三方的入站集成所使用。
*   确保在出现问题时可以回滚。
*   mLab 最近被 MongoDB Inc .收购，所以在接下来的 8 个月左右，我将被迫把我的数据库迁移到他们的 Atlas 服务。

## 我仍然需要回答的问题

目前我脑子里只有上面的高层次目标和约束。我还没有做任何详细的迁移规划，仍然有许多问题没有答案，我需要做出决定。

我将利用这一系列的帖子，在我制定和执行它们之前与你讨论这些。为了让你体验一下，我想我需要回答以下几个问题:

*   我应该用什么替换 MongoDB 作为我的主数据库？可能是 DynamoDB，但我应该考虑哪些替代方案？
*   如何将现有数据从 MongoDB 映射和迁移到 DynamoDB/alternative？
*   在转换期间，如果 MongoDB 和 DynamoDB 都在使用，我需要将数据更改同步到这两个数据库吗？
*   我可以在我的 monolith API 中识别出哪些服务边界，并将其划分为微服务？
*   我应该对每个微服务使用单一回购还是单独回购？
*   我如何一次将单个 API 请求重新路由到我的新 APIGW/Lambda 函数(例如，使用[扼杀者模式](https://www.jeremydaly.com/serverless-microservice-patterns-for-aws/#strangler)？
*   我将如何测试新的 APIGW+Lambda API 端点？
*   我应该如何将身份验证和授权从 Express 应用程序中现有的 PassportJS 实现转移到 API Gateway？我应该使用 Cognito，一个定制的授权者还是其他什么？

## 我的第一个重大决定

在软件设计中(也可能在生活中)，我更喜欢推迟一个重大的决定，直到我不做出决定就无法在关键的道路上前进的时候。因此，尽管我对上面列出的所有问题都有想法和倾向，但我还没有做出明确的决定，也不打算这么做。

我需要做出决定的第一个问题是:

我是从 MongoDB 到 DynamoDB 的迁移开始，还是从 monolith Express 到 APIGW+Lambda 代码重写开始？

两种选择都会让我走上一条完全不同的道路，所以我需要仔细考虑。让我们深入探讨两者的利弊(✅和❌)

### 选择 1:从改变数据库开始

*   每当系统处于负载状态时，✅ MongoDB 就是主要的瓶颈。
*   ✅ mLab 是我最大的一个云项目，所以越早移除它，我就能越早实现这些成本节约。
*   ✅我有大约 8 个月的期限从 mLab 迁移到 Atlas。如果到那时我已经没有 MongoDB 了，我可以完全避免这种情况。
*   ❌ DynamoDB 的索引和查询模型与 MongoDB 的非常不同，因此需要大量的分析来确定最佳设计。
*   ❌当两个数据库并行运行时，如何管理转换期是一项复杂的工作，并且存在数据不同步的风险。
*   ❌:我需要对遗留 monolith 代码库进行全面更新，以便用 DynamoDB API 调用替换 mongose(node . js MongoDB 数据映射器库)调用。当这些代码转移到 Lambda 函数时，可能需要再次重构。
*   ❌将第一段 DynamoDB 代码投入生产涉及到许多步骤(和风险),例如，设计目标模式和索引，编写迁移数据的脚本，提出并行运行计划，以及更新应用程序代码以更改 API 端点来使用新的 DB。

### 选项 2:从 ExpressJS 到 API Gateway/Lambda 重写开始

*   ✅理解和重构 JavaScript/Node.js 代码是我在数据库建模和数据迁移方面最强的技能之一。所以我很有信心我能很快完成。
*   express 应用程序几乎已经是无状态的了，所以在将 API 请求路由到新代码时，我对不一致的状态没有什么担心。
*   ✅:一旦完成，我就不需要修补 EC2 应用服务器实例了(mLab 为 MongoDB 副本集处理这个问题)。
*   ✅:将第一个 API Gateway+Lambda 代码投入生产使用并不需要很多步骤，所以我可以很快取得胜利。
*   express 应用程序运行在 Node.js v6 上，其支持将于 2019 年 4 月终止。升级到 v8 将会自动到来，因为我会逐步将代码转移到 Lambda。
*   ❌假设 MongoDB 在一个 VPC 中，我将需要 VPC 启用我的 Lambda 函数，并忍受由此带来的性能/伸缩限制。
*   ❌:在所有 API 端点迁移完毕，我可以关闭 EC2 实例和负载均衡器之前，我不会实现任何显著的云账单节省。即使这样，EC2 实例的成本也比 mLab 法案低。

权衡了两种方法的利弊后，我决定采用第二种方法——从重写代码开始。

这将允许我以更小的块更快地将代码投入生产。选择这条道路的另一个令人放心的因素是，它类似于 AWS 无服务器英雄崔琰在 Yubl 的无服务器架构之路上选择的道路。我希望将我从 Yan 的优秀的[生产就绪无服务器课程](https://bit.ly/production-ready-serverless)中学到的许多技术作为这次迁移的一部分。

## 下一步

在开始迁移之前，我将为迁移过程中创建的资源设置两个新的 AWS 帐户(开发/暂存和生产)。目前，我的试运行和生产环境都在一个账户中，还有几个不相关的静态网站([不要在家里这样做，孩子们](https://winterwindsoftware.com/managing-separate-projects-in-aws/))。然而，我想让我的房子井井有条，并隔离这些前进，所以我将使用 [AWS 组织](https://docs.aws.amazon.com/organization)来帮助构建我的帐户。

在此之后，我将着眼于在 monolith API 中确定服务边界的[以得出一系列微服务，我可以按照逻辑顺序一个接一个地提取这些微服务。](https://blog.binaris.com/how-to-migrate-existing-monoliths-to-serverless/)

在我的下一篇文章中，我将分享我的调查结果，以及更多关于“原样”架构的信息。

你有问题或建议，或不同意我的任何推理吗？你能看出我遗漏了什么吗？如果是这样，太好了！这就是我这么做的原因🙂。请在下面的评论中告诉我。

✉️ **如果你想尽快获得这个系列的未来更新，你可以[在这里](https://winterwindsoftware.com/serverless-migration-journal/#signup)订阅。**

* * *

您还可能会喜欢:

*   [担心无服务器带走](https://winterwindsoftware.com/concerns-that-serverless-takes-away/)
*   [“无服务器”的不同定义](https://winterwindsoftware.com/serverless-definitions/)
*   [无服务器词汇表](https://winterwindsoftware.com/serverless-glossary/)

*原载于 winterwindsoftware.com*。