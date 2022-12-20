# 在 Azure 中分解监控

> 原文：<https://dev.to/documentednerd/breaking-down-monitoring-in-azure-2cm3>

所以我们来谈谈 Azure 中的监控，老实说这是一个让大多数人开始变成“车灯前的小鹿”的话题。原因是大多数高管喜欢说“我们需要一个伟大的监控故事”，但监控是一个巨大的话题，大多数人不知道从哪里开始。

事实是，这是一个巨大的多方面的主题，有各种各样的解决方案，可以以各种方式应用。所以我想收集一些资源来帮助你，如果你想知道哪条路是向上的。

MSLearn:通过各种主题的文章、动手实验和视频提供指导学习。鉴于您的提问，我认为您可能会感兴趣的一些关键问题是:

*   [现场可靠性工程介绍(SRE)](https://docs.microsoft.com/en-us/learn/modules/intro-to-site-reliability-engineering/)
*   [Azure 中的效率和运营设计](https://docs.microsoft.com/en-us/learn/modules/design-for-efficiency-and-operations-in-azure/)
*   [蔚蓝政府简介](https://docs.microsoft.com/en-us/learn/modules/intro-to-azure-government/)
*   [管理 Azure 中的基础设施资源](https://docs.microsoft.com/en-us/learn/paths/administer-infrastructure-resources-in-azure/)
*   [预测 Azure 的成本并优化支出](https://docs.microsoft.com/en-us/learn/modules/predict-costs-and-optimize-spending/)
*   [使用基于角色的访问控制保护您的 Azure 资源(RBAC)](https://docs.microsoft.com/en-us/learn/modules/secure-azure-resources-with-rbac/)
*   [Azure 安全简介](https://docs.microsoft.com/en-us/learn/modules/intro-to-security-in-azure/)

在监控方面，我们有几种产品和服务是提供这种支持的核心:

*   [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/) :提供一个复杂的工具，用于收集和分析遥测数据，并提供收集指标、生成警报和构建定制仪表板的能力，以监控您的环境。
    *   [什么是 Azure Monitor？](https://docs.microsoft.com/en-us/azure/azure-monitor/overview)
    *   [快速入门–监控 Java 应用](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/java-quick-start)
    *   [快速入门–收集 Azure 虚拟机上的数据](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-collect-azurevm)
    *   [快速入门–在 Linux 虚拟机上收集数据](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-collect-linux-computer)
    *   [教程–使用日志分析分析数据](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-viewdata)
    *   [教程–基于 Azure Monitor 的警报](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-response)
    *   [使用 Azure Monitor 进行持续监控](https://docs.microsoft.com/en-us/azure/azure-monitor/continuous-monitoring)
*   [Azure Network Watcher](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview) :允许监控、诊断和查看 Azure 虚拟网络上的指标。
    *   [教程–监控两台虚拟机之间的网络通信](https://docs.microsoft.com/en-us/azure/network-watcher/connection-monitor)
    *   [如何配置网络观察器](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-create)
*   [日志分析](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview):这是驱动许多其他服务的后端，包括提供对定制日志摄取的支持以监控解决方案。
*   [应用洞察](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview):这提供了对应用代码的健壮监控，包括事件、请求、性能和响应。它使得在应用程序功能级别更容易看到用户如何使用您的应用程序，以及您如何识别问题或需要改进的地方。
*   [devo PS 概述](https://docs.microsoft.com/en-us/azure/azure-monitor/app/detect-triage-diagnose)
*   [安全中心](https://docs.microsoft.com/en-us/azure/security-center/):使用日志分析监控您的解决方案的潜在安全风险。
    *   [快速入门:使用 Azure 订阅安全中心标准](https://docs.microsoft.com/en-us/azure/security-center/security-center-get-started)
    *   [快速启动:将您的 Linux 虚拟机装载到安全中心](https://docs.microsoft.com/en-us/azure/security-center/quick-onboard-linux-computer)
    *   [教程–使用安全中心保护资源](https://docs.microsoft.com/en-us/azure/security-center/tutorial-protect-resources)
    *   [教程-应对安全事件](https://docs.microsoft.com/en-us/azure/security-center/tutorial-security-incident)
    *   [使用安全中心进行事故响应](https://docs.microsoft.com/en-us/azure/security-center/security-center-incident-response)
*   [Azure 活动日志](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/activity-logs-overview):这跟踪在订阅上执行的所有动作，以确保对订阅级别事件的洞察。

此外，我们在一个名为 9 频道的网站上有各种主题的视频

*   网络性能监视器:
    *   [网络性能监视器概述【6:05】](https://channel9.msdn.com/Series/Operations-Management--Security/Network-Performance-Monitor?term=monitor&lang-en=true)
    *   [网络性能监视器深度剖析[30:19]](https://channel9.msdn.com/Series/Operations-Management--Security/Network-Performance-Monitor-Deep-Dive?term=monitor&lang-en=true)
*   Azure 监视器
    *   [Azure Fridays–Azure Monitor[13:14]](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Monitor?term=monitor&lang-en=true&pageSize=15&skip=15)
    *   【Azure Monitor 入门[5:12]
    *   [Azure Monitor 活动警报[3:41]](https://channel9.msdn.com/Blogs/Azure-Monitoring/Azure-Monitor-Activity-Log-Alerts)
    *   [红衫军发展之旅-Azure Monitor[6:29]](https://channel9.msdn.com/events/Microsoft-Azure/Azure-Red-Shirt-Dev-Tour-London-2017/Azure-Monitor?term=monitor&lang-en=true&pageSize=15&skip=30)
*   Azure 日志分析
    *   [Azure Fridays–Azure 日志分析[13:02]](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Log-Analytics?term=log%20analytics&lang-en=true)
    *   [日志分析有什么变化[5:08]](https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics?term=log%20analytics&lang-en=true)
    *   [改进的 Azure Log Analytics:具有机器学习和更多功能的强大查询语言[1:06:20]](https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3269?term=log%20analytics&lang-en=true)
*   一般监控:
    *   [利用 Azure Application Insights 持续监控开发运营&日志分析【1:23:39】](https://channel9.msdn.com/events/Build/2018/BRK2143?term=log%20analytics&lang-en=true)
    *   [应用监控和反馈回路](https://mva.microsoft.com/en-US/training-courses/18253?l=w7fFvp7rE_805167344)

【Kusto 日志分析查询语言的链接

[通过 CLI 部署 ARM 模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli)

[完整监控解决方案的链接](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/azure-networking-analytics)

[日志分析的日志链接和捕获数据的详细信息](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)