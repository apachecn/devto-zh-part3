# Azure 入门(开发者视角)

> 原文：<https://dev.to/documentednerd/getting-started-with-azure-developer-perspective-4ab1>

所以有一个我最近经常收到的常见问题，那就是“我想学习 Azure，我该从哪里开始？”这最终是一个非常合理的问题，因为尽管云已经改变了数字世界的大部分，但仍有一些组织只是最近才开始采用它。

人们选择采用云的原因有很多，可扩展性、成本、灵活性等等。但是对于今天的帖子，我将重点关注这样一个想法，即你已经决定去 Azure 云，并且正在寻找资源来加速。所以我想在这里提供这些:

**MS Learn** :该网站提供视频、阅读和演示，可以帮助学习这类材料:

*   [Azure Fundamentals](https://docs.microsoft.com/en-us/learn/paths/azure-fundamentals/) :这个课程路径包含几个课程，侧重于初级水平的各种主题，包括架构、监控、成本、存储、计算和安全。
*   管理 azure 中的资源:这个课程提供了管理你在 Azure 中创建的资源的基础知识的高级介绍。
*   通过 RBAC 保护你的 Azure 资源:基于角色的访问控制是 Azure 合规性的基石。这里的意图是，您必须在开始时设置适当的治理，以防止在 azure cloud 中工作的团队支出失控。
*   [使需求与 Azure 中的云类型和服务模型保持一致](https://docs.microsoft.com/en-us/learn/modules/align-requirements-in-azure/):本课程提供了一些很好的见解，帮助您确定哪种云策略最适合您，是混合云还是完全托管云。
*   Azure 中的效率和运营设计:这是一门很好的课程，提供了如何进行效率架构的基础知识。对于许多内部部署开发人员来说，观念上最大的变化之一是，在云中，您为最小化和扩展能力而设计，而不是为“最坏的情况”而设计。
*   Azure 中的安全性设计:许多开发人员考虑的最后一件事，但它应该是第一件。任何组织都不能将安全性视为事后的想法。
*   在 Azure 中为性能和可伸缩性而设计:云的最大好处是可伸缩性，这不是某种刚刚发生的魔法，而是一种你需要接受的能力。
*   一个伟大的 azure 架构的支柱:一个关于在 azure 云中构建一个伟大架构的支柱和考虑事项的好课程。
*   [通过条件访问保护你的 azure 资源](https://docs.microsoft.com/en-us/learn/modules/secure-azure-resources-with-conditional-access/):一个关于如何为你的 azure 资源设置条件访问的很棒的短期课程。
*   [使用 Azure 服务总线](https://docs.microsoft.com/en-us/learn/modules/implement-message-workflows-with-service-bus/)实现基于消息的通信工作流:许多人谈论“微服务”架构，该模式的最大租户之一是消息传递和排队的异步通信实践，这将带您创建一个使用服务总线的通信模式。
*   [使用 Azure 虚拟网络分发您的服务，并通过使用虚拟网络对等来集成它们](https://docs.microsoft.com/en-us/learn/modules/integrate-vnets-with-vnet-peering/):基于软件的网络是所有云中最重要的部分之一，是理解云中通信所必需的。
*   [使用 blob 存储层优化存储性能和成本](https://docs.microsoft.com/en-us/learn/modules/optimize-archive-costs-blob-storage/):每个云的另一个关键部分是使用云优化存储，它支持可扩展的存储平台，为许多软件应用程序提供核心。

**MS Learn for Specific Services:**许多人在想到云时会想到几种常见的服务，我想在这里提供一些资源来帮助他们:

*   使用 azure 功能创建无服务器平台:无服务器平台风靡一时，这是有原因的，本课程将向您介绍这个领域中 Azure 初级产品的基础知识。
*   [保护你的 azure 虚拟机磁盘](https://docs.microsoft.com/en-us/learn/modules/secure-your-azure-virtual-machine-disks/):虚拟磁盘是许多应用程序的关键部分，应该始终得到适当的保护，下面是如何做的指南。
*   Azure 中的安全、责任和信任:说得够多了。
*   [预测 Azure 的成本并优化支出](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/):构建云计算最重要的部分之一是监控您的支出，并控制成本。这是一门关于如何实现这一目标的伟大课程。
*   [使用 Azure Policy 应用和监控基础设施标准](https://docs.microsoft.com/en-us/learn/modules/intro-to-governance/) : Azure Policy 是 RBAC 的一个关键部分，它使您能够控制和监控您的团队可以创建哪些类型的资源。
*   [使用应用网关对您的 web 服务流量进行负载平衡](https://docs.microsoft.com/en-us/learn/modules/load-balance-web-traffic-with-application-gateway/):Web 应用防火墙(WAF)正在成为支持 Web 应用流量监控的关键网络设备，本课程将带您了解相关注意事项。
*   [通过命令行和代码](https://docs.microsoft.com/en-us/learn/modules/copy-blobs-from-command-line-and-code/)将 blob 从一个容器或存储帐户复制并移动到另一个容器或存储帐户:快速简单地演示如何在存储帐户内和之间移动 blob。
*   在 azure 中管理容器 : Docker 容器正成为许多现代应用的关键部分，本课程将带您了解如何使用我们的 PaaS 工具管理 Azure 上的容器。
*   [从 azure blob 存储容器收集指标](https://docs.microsoft.com/en-us/learn/modules/gather-metrics-blob-storage/):监控是云应用的另一个关键部分，这里有一个如何监控与存储相关的指标的演练。

**EDX 课程:** EDX 是一个很棒的网站，有很多制作精良的课程，有很多 Azure 和 Cloud 的选项，这里有一些我认为相关的，但不是详尽的列表。

*   [设计分布式应用](https://www.edx.org/course/architecting-distributed-cloud-applications-2):许多人对云犯的一个常见错误是，他们认为它“只是另一个数据中心”，但这是不正确的。为了构建高效且可扩展的应用程序，需要将它们设计为利用分布式计算的架构。本课程很好地展示了如何确保你的架构能够以分布式方式工作。
*   微软 Azure 存储(Microsoft Azure Storage):一门关于使用 Azure 存储的基础知识的伟大课程。
*   微软 azure 虚拟机(Microsoft Azure Virtual Machines):虚拟机是 Azure 的基石，它提供了许多选项来有效地构建横向扩展。这是对 Azure 中最基本服务的一个很好的介绍。
*   [微软 Azure App Service](https://www.edx.org/course/microsoft-azure-app-service-3):Azure 中最受欢迎的服务，App Service 使开发者能够部署和配置应用，而不用担心机器在幕后运行。一个很好的概述。
*   [微软 Azure 虚拟网络](https://www.edx.org/course/microsoft-azure-virtual-networks-3):正如我上面提到的，基于软件的网络是云所需的关键部分之一，这很好地介绍了如何利用它。
*   [Azure 中的数据库](https://www.edx.org/course/databases-in-azure-3):云的另一个关键组件是数据库，这谈到了利用数据库的平台即服务产品来消除维护虚拟机的开销的选项。
*   [Azure 安全性和合规性](https://www.edx.org/course/azure-security-and-compliance-3):安全性也是一个关键因素，因为数字威胁在不断演变，Azure 提供了许多工具来保护您的工作负载，这是每个架构的重要组成部分。
*   构建你的 azure 技能工具包:一个很好的初学者课程，教你如何快速掌握 Azure 技能。

其他工具和资源，我推荐以下内容:

*   [Azure 实例信息](http://www.azureinstances.info/)–提供虚拟机大小的详细分类和统计数据。
*   [开始使用 Terraform 和 Azure](https://learn.hashicorp.com/terraform/?track=azure#azure)-这是一个关于如何使用 Terraform 管理云基础设施的很好的演练。
*   [安装 Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)——一个必不可少的工具是 Azure 命令行界面，可以在这里下载。
*   [将 Azure CLI 连接到 Azure Government](https://docs.microsoft.com/en-us/azure/azure-government/documentation-government-get-started-connect-with-cli)–对于那些使用政府云的人来说，还有一些额外的步骤来使 Azure CLI 工作，这些步骤在这里进行了概述。
*   Azure Storage Explorer-一个基本工具，它允许您探索云中所有不同存储解决方案的内容。
*   Azure 架构中心:查看云参考架构的绝佳网站。

这些只是众多资源中的一部分，可以帮助你开始使用 Azure 并学习构建云应用。这不是一个详尽的列表，所以如果你有你认为有用的资源，请在下面的评论中发布。