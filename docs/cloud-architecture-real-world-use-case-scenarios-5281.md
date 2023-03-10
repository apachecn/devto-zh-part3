# 云架构:真实世界的用例场景

> 原文：<https://dev.to/eisk/cloud-architecture-real-world-use-case-scenarios-5281>

虽然随着云技术的出现，构建一个可扩展的系统变得容易得多，但确定合适的系统可能具有挑战性。通常这可能需要在建筑领域积累多年的丰富经验。

在这里，我们试图总结出一个顶级技术的列表，这些技术可以用于根据真实世界的用例场景高效地构建可伸缩系统。

为了使列表简单，有意忽略了一些基本的考虑因素，例如编程/脚本语言、操作系统选择、附加工具等。

## 示意图

[![Awesome Cloud Architecture](img/0ff5fab50d80da3721df0a63bd0eba24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gk-GqWtD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t81jthbk2cbe0pdkq0n3.png)

## 表

| 类型 | 目的 | 用例 | 技术 |
| --- | --- | --- | --- |
| 客户端 | 客户端框架 |  | [反应堆](http://reactjs.org) |
| - | 类型系统 |  | [打字稿](https://www.typescriptlang.org) |
| - | 设计系统 |  | 蚂蚁 |
| 后端 | 服务器端 API 框架 |  | [ASP.NET 核心网络应用编程接口](https://docs.microsoft.com/en-us/aspnet/core/web-api) |
| - | 并发框架 |  | [。网络任务 API](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/task-based-asynchronous-programming) |
| - | ORM 框架 | 操作 RDMBS 数据 | [实体框架核心](https://docs.microsoft.com/en-us/ef/core/) |
| - | 身份框架 | 管理用户身份 | [。网络核心标识](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity) |
| - | 云访问框架 | 云存储访问、日志记录等 | [Azure 开发者工具](https://azure.microsoft.com/en-us/tools/) |
| - | Web 套接字 | 发送月度总结报告 | 信号 |
| 服务 | Web 应用程序和 API 部署 | 托管 web 应用程序 | [Azure 应用服务](https://azure.microsoft.com/en-us/services/app-service/) |
| - | 身份服务 | 管理用户身份 | [Azure 活动目录](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis) |
| - | 遥测事件摄取 | 用户活动日志(接收) | [Azure 活动中心](https://azure.microsoft.com/en-us/services/event-hubs/) |
| - | 企业搜索 | 产品搜索 | [Azure 搜索](https://azure.microsoft.com/en-us/services/search/) |
| - | 基于前缀的搜索 | 产品搜索(带前缀) | [Azure 搜索](https://azure.microsoft.com/en-us/services/search/) |
| - | 建议 | 产品推荐 | [天蓝色 ML](https://azure.microsoft.com/en-us/services/machine-learning-service/) |
| - | 服务器到客户端通知 | 发送月度总结报告 | [Azure 通知中心](https://azure.microsoft.com/en-us/services/notification-hubs/) |
| - | 报告处理 | 发送月度总结报告 | [Azure 调度器](https://azure.microsoft.com/en-us/services/scheduler/) |
| - | 报告处理 | 每日操作报告 | [具有 Power BI 的 Azure Sql 数据库](https://docs.microsoft.com/en-us/power-bi/service-azure-and-power-bi#azure-sql-database-and-power-bi) |
| - | 救济品 | 应用程序生命周期管理 | [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) |
| 储存；储备 | 分析学 | 用户活动日志 | [Azure cosmos db:Cassandra as-a-service](https://azure.microsoft.com/en-us/blog/dear-cassandra-developers-welcome-to-azure-cosmosdb/) |
| - | 报告 | 每日操作报告 | [具有 Power BI 的 Azure Sql 数据库](https://docs.microsoft.com/en-us/power-bi/service-azure-and-power-bi#azure-sql-database-and-power-bi) |
| - | 报告 | 月度总结报告 | [Azure Cassandra 即服务](https://azure.microsoft.com/en-us/blog/dear-cassandra-developers-welcome-to-azure-cosmosdb/) |
| - | 二进制内容 | 用户个人资料图片，徽标 | [Azure Blob 存储](https://azure.microsoft.com/en-us/services/storage/blobs/) |
| - | CDN 缓存 | 用户个人资料图片、徽标(缓存) | [Azure CDN](https://azure.microsoft.com/en-us/services/cdn/) |
| - | 在内存缓存中 | 用户会话数据 | [用于 Redis 的 Azure 缓存](https://azure.microsoft.com/en-us/services/cache/) |
| - | 键值对 NoSQL 商店 | 购物车 | [Azure 桌面储物](https://azure.microsoft.com/en-us/services/storage/tables/) |
| - | 记录 NoSQL 商店 | 产品目录 | 蓝色宇宙数据库 |
| - | 图表 NoSQL 商店 | 社交网络图 | [Azure CosmosDB: Gremlin API](https://docs.microsoft.com/en-us/azure/cosmos-db/graph-introduction) |
| - | 关系型数据库管理系统 | 财务数据-资产、负债、收入 | [Azure Sql 数据库](https://azure.microsoft.com/en-us/services/sql-database/) |

## 令人敬畏的云架构⭐开源项目

EISK 是一个开源项目，旨在为开发人员提供资源，在令人敬畏的云架构之上构建可扩展的应用程序。

已经发布了一个 [web api](https://github.com/EISK/eisk.webapi) 项目来展示实现的最佳实践和更多。

通过在 [github](https://github.com/EISK/eisk) 中为项目添加书签来保持联系。

尽情享受吧！