# 将日志记录作为服务

> 原文：<https://dev.to/tpetrov9/logging-as-a-service-5854>

# 告别日志梳理和 shell 脚本

**在当今的 IT 环境中，每个运行时每天生成的数据量都是巨大的，运营团队很难理解这些数据并从中获取价值。太多具有不同接触点的应用程序和越来越多的产品安装及其关系增加了 IT 环境的复杂性。然而，有一个解决方案:统一的运营智能可以快速解决性能问题，提高客户满意度，简化关系和简化系统。**

*软件公司首席顾问 Logeshwaran Kannan Ponnurangam 和高级顾问 Venkata Santosh saj Jan Alla*

## 实时洞察 IT 环境

IT 组织努力提供最佳的用户体验来支持业务需求，并主要关注构建快速和可扩展的应用程序。通常不太重视操作和支持。在我们作为 webMethods 顾问的工作中，我们看到许多系统管理员和支持团队花费大量时间梳理日志并运行 shell 脚本来理解问题的根源。

如果有一种更快的方法来帮助解决这个问题……快速解决性能问题、提高客户满意度、简化关系和简化系统，会怎么样？

**统一运营智能**诞生于 Software AG 的 WIRED 2018 创新大赛期间。它在一个定制的框架中使用 ELK*(弹性搜索、Logstash & Kibana 和 FileBeat)以及 Software AG Platform Manager (SPM ),将每个运行时发出的日志与从 webMethods Integration Server 的 SPM APIs 收集的健康统计信息相关联。

还集成了**交互式通用消息监控**，以检索服务器资产和运行状况的实时统计数据。任何 Software AG 客户都可以使用该工具；开发它是因为 Universal Messaging Enterprise Manager(EM)是一个胖客户端，不提供查看整体统计数据的灵活性。相反，你需要了解每一项资产并获得详细信息。EM 也不提供搜索和警报功能。交互式通用消息监视提供了查看整合统计信息的轻量级访问，并为您提供了以与集成服务器软件包相同的方式进行安装的灵活性。

统一操作智能框架的另一个扩展将 webMethods Integration Server 中用流语言编写的接口关联起来。

该解决方案提供了完整的端到端实时可见性，有助于更好地了解系统及其逻辑关系。

[![](img/4d2a41e740ed30cd973241a124248c5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MPHRGJ0D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10221817/Unified_Operational_Intelligence_1.png/2a8250f3-e13f-4845-9399-dc346a834fab%3Ft%3D1547737632362) 

_ **图 1** :解决方案架构 _

Kibana 中丰富的交互式统一操作智能仪表板将不同运行时组件发出的错误关联起来，并包含日志级别、内存统计、响应时间、运行服务、运行状态、CPU 统计、服务错误和 JVM 线程的图形。您可以根据需要使用任何维度过滤仪表板。根据选定的过滤器，仪表板将采用图表中的维度。一切都在实时展开。

[![](img/fcbddb2364a3b98a4493b4a7f0374516.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tbPw_WO_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10221817/Unified_Operational_Intelligence_2.png/755e9c3b-2e4d-423a-bf27-247aa0397582%3Ft%3D1547737639715) 

_ **图 2** :关于日志级别和运行状况统计的统一运营智能仪表板 _

您的环境中的可用运行时之间的关系可以实时生成，并在此给出详细的视图。该图也可以根据您的需要进行定制。

[![](img/fa679de26c9e2166e911bd0535b97142.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c_Cly56r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/10221817/Unified_Operational_Intelligence_3.jpg/025255bf-e960-4011-b547-95f0dec159dc%3Ft%3D1547737643443) 

_ **图 3** :关系图 _

## 从日志中获取值

统一运营智能通过监控每个应用生命周期和交易，将客户体验、应用性能和业务成果联系起来。它使您能够基于智能分析做出有效的决策。您可以最大限度地减少对专家理解和解决问题的依赖。

Unified Operational Intelligence 目前支持 webMethods Integration Server、Universal Messaging、My webMethods Server 和 Terracotta。要了解更多信息，请发送电子邮件至[consulting-sales@softwareag.com](//mailto:consulting-sales@softwareag.com)

**“ELK”是三个开源项目的缩写:Elasticsearch、Logstash 和 Kibana。Elasticsearch 是一个搜索和分析引擎。Logstash 是一个服务器端数据处理管道，它同时从多个来源获取数据，对其进行转换，然后将其发送到像 Elasticsearch 这样的“stash”。Kibana 允许用户在 Elasticsearch 中用图表和图形可视化数据。*