# 配置安全服务

> 原文：<https://dev.to/cheahengsoon/configure-security-services-o47>

**Azure Monitor**
Azure Monitor 使用两种基本的数据类型——指标和日志:

*   您可以通过使用查询来分析 Azure Monitor 收集的日志数据，以快速检索、整合和分析收集的数据
*   Azure Monitor 提供了一个整合的管道，用于将任何监控数据路由到 SIEM 工具中
*   安全中心自动收集、分析和整合来自 Azure 资源的日志数据![Alt text of image](img/6a2e2bce25f40247dd2423677e293ea7.png)

**配置 Azure 日志分析**

*   Azure 日志分析服务帮助您收集和分析由您的云和内部环境中的资源生成的数据
*   日志分析的核心是日志分析工作区，它托管在 Azure 中
*   数据源和解决方案创建不同的记录类型，每种类型都有自己的属性集
*   连接的源是生成日志分析收集的数据的计算机和其他资源![Alt text of image](img/366f5800a0f92ca50c974c8e6671dcdf.png)

**配置诊断日志记录和日志保留**

*   Azure Monitor 提供两种类型的诊断日志:
    *   租户日志
    *   资源日志
*   这些日志的内容因 Azure 服务和资源类型而异
*   这些日志不同于活动日志，活动日志提供了对 Azure 资源管理器在订阅资源上的操作的洞察
*   来宾操作系统诊断日志是由运行在虚拟机或其他受支持资源类型![Alt text of image](img/975ce059606d6d37c5c8ae7a2b0051cb.png)中的代理收集的日志