# webMethods 10.4:新特性和改进

> 原文：<https://dev.to/techcommunity/webmethods-10-4-new-features-and-improvements-2kb3>

# 查看集成、API 管理等方面的最新增强功能

Software AG 的 webMethods 使您能够在顶级混合集成平台上构建解决方案。您可以集成基于云的应用和内部系统，无需编码，使用微服务风格的架构来更快地交付和扩展新的集成，并利用 API 管理来促进和保护对 API 的访问。将现有技术和新技术与物联网(IoT)相集成，并使用许多不同的部署选项来推动敏捷性和创新。

webMethods 套件提供了广泛的功能，包括:

*   应用程序和合作伙伴集成
*   API 管理
*   B2B 集成
*   托管文件传输
*   构建微服务
*   高速信息传递
*   内存缓存

有了 webMethods，您就有了一个可扩展、可伸缩且可靠的主干，既可以在云中运行，也可以在内部或混合场景中运行，为云、移动、API 和物联网提供无缝集成。我们最新发布的 webMethods 10.4 对产品集的许多功能方面进行了重大增强。请继续阅读，了解更多信息。

## webMethods 集成

webMethods Integration Server 是整个 webMethods 套件背后强大的核心引擎，涵盖业务流程管理、B2B 集成、托管文件传输、API 管理等等。

我们的混合集成平台易于扩展，能够将您的内部应用轻松集成到 SaaS 应用，与编程模型无关，支持多容器。作为一个容器，该平台附带了数百个有用的预构建 API、服务、框架、事件驱动架构和内存数据网格支持，以节省您的时间。

该平台拥有全系列的连接器来支持最现代的标准，如 OData、REST 和基于 SOAP 的 web 服务的完整补充。映射和转换功能是内置的，在集成大数据源时尤其有用。

这个版本的主要特性包括支持 GraphQL 和 Integration Server。Integration Server 使开发人员能够创建描述各种类型和字段的 GraphQL 模式，以便开发符合 GraphQL 标准的 API。Integration Server 提供了对 JSON 模式的本地支持，允许开发人员基于 JSON 模式创建 JSON 文档类型。Software AG Designer 可用于将 REST API 描述符(Swagger)发布到 webMethods API 网关。

Integration Server 在创建 REST APIs 时提供了对 Swagger 2.0 标准的增强支持。集成服务器经过认证，可以使用 QPID JMS 客户端库与支持 AMQP 协议的 Java 消息服务(JMS)提供程序进行通信。

webMethods 微服务运行时降低了磁盘大小要求，并缩短了启动时间。我们为微服务运行时提供了一个示例 OpenShift 脚本，可以对其进行定制以满足用户的需求，因此您可以创建微服务运行时 pod 并使用 OpenShift 对其进行编排。

web methods active transfer 中的新 UI 支持动作、日志和代理服务器设置的增强。Software AG Designer 服务开发支持一种称为部署状态的新视图。该视图显示了云部署历史，包括部署状态和调试问题日志。

webMethods CloudStreams 服务器支持在创建连接时关联一个或多个由 SaaS 应用程序或后端系统支持的身份验证方案。它支持由 Salesforce 为消息持久性提供的 Salesforce 重播事件机制，以检索和处理由 Salesforce 在支持的保留窗口内发送的丢失事件。

CloudStreams 服务器支持使用 JSON Web Token (JWT)的 OAuth 2.0 认证流。JWT 支持跨安全域共享身份和安全信息。

用户可以使用 Command Central web 用户界面和命令行界面窥探通用消息传递通道或队列上的事件，并从通道或队列中清除事件。用户还可以使用 Command Central web 用户界面和命令行界面在通用消息传递通道或队列上发布事件，包括 Protobuf 事件。使用“水平可伸缩性”功能的通用消息传递会话现在支持自动(重新)连接到可伸缩性集中的领域服务器，这些服务器在会话创建或初始化时脱机或不可用。

**webMethods API 管理**

Software AG 的 webMethods API 门户提供了一个全面的解决方案，允许开发人员社区查找、了解、讨论和测试组织的 API。API Portal 不仅向组织内外的开发人员公开 API 文档，还管理开发人员入职流程，并允许开发人员将公开的 API 用于新的和创造性的用途。

webMethods API Gateway 安全地向消费者、第三方开发者和其他合作伙伴公开您的 API，以便在 web、移动和物联网应用程序上使用。webMethods Microgateway 是一个独立的产品——轻量级、敏捷且快速。它与 webMethods API Gateway 一起工作，或者作为一个独立的解决方案来控制 API 对分布式环境中微服务的访问，并支持提供独立和边车部署的服务网格架构。

有许多 API 文档增强，包括 API Gateway 中增强的导入和导出支持，以包含所有资产和配置，以便用户可以轻松地跨实例移动配置。API Gateway 中的暂存和升级支持已得到增强，包括所有资产和配置，因此用户可以轻松地在实例间移动配置。对于应用程序识别，我们已经保护它使用通过自定义 HTTP 头发送的证书。自定义 HTTP 头可以配置为 API 网关管理中的扩展设置。

此外，API 提供者可以配置为并行调用多个 API，作为一个混搭步骤的一部分，并聚合传递给下一个步骤的响应。类似地，任何混搭步骤的响应都可以被聚合，并作为单个输出发送给客户端。API Gateway 可以在 Kubernetes 环境中运行，该环境为服务的自动化部署、扩展和操作提供了一个平台。

在此版本中，API Gateway 提供了一个专用屏幕来显示向其注册的微网关组。API Gateway 用户现在能够检索组内可用的 microgateway 的详细信息以及资产和配置列表。批准流程的用户入职得到增强，包括多级批准流程。

API gallery 页面得到了增强，可以根据 API 发布到的社区来显示 API 分组。API Portal 现在允许用户与特定的用户或团队列表共享他们注册的应用程序。

webMethods Microgateway 为 Kubernetes 提供健康和就绪检查以及 Prometheus 指标等支持。Microgateway 配置功能已得到增强，它允许使用单个 YAML 设置文件来提供实例。Microgateway 支持 API 作用域以及资源和方法级别的策略实施。微网关配置现在可以包括运行时别名配置。

CentraSite 用户现在可以触发 API 从一个 API 网关实例到另一个实例的升级。当环境中有多个可用的 API 网关实例时，您可以选择 API 必须升级到的所需实例。

**套件增强功能**

webMethods 套件包含了跨多个产品改进功能的增强功能。

在 webMethods 10.4 之前，Command Central 在应用了所有配置更改后，重新启动了所有运行时实例和相关的 platform manager 安装。当您将 Command Central 10.4 与 Platform Manager 10.3 或更高版本一起使用时，此行为会更新。在此版本中，可以将 Command Central 设置为仅在运行时实例需要重新启动才能应用更新时才重新启动运行时实例。

Software AG Command Central 和 Platform Manager 10.4 可以在未应用配置设置时将模板应用程序设置为失败。为了确保向后兼容性，旧的行为是默认行为。Software AG Command Central 使您能够使用 CLI 命令为 Platform Manager 10.0 或更高版本上托管的产品实例生成模板。

GitHub 现在提供了使用 Command Central 安装和基本配置分析引擎和 web 服务数据收集器的默认模板。任务引擎现在支持在单独的数据存储中归档任务实例数据。业务控制台与运行在集成服务器上的协同定位任务引擎的集成已经稳定。数据库组件配置器工具被重写以提高可靠性和性能。

有关更多详细信息，请参见 webMethods 10.4 发行说明，网址为[https://documentation . software ag . com/web methods/WM suites/WM suite 10-4/rel Notes/10-4 _ web methods _ Release _ Notes . pdf](https://documentation.softwareag.com/webmethods/wmsuites/wmsuite10-4/relnotes/10-4_webMethods_Release_Notes.pdf)