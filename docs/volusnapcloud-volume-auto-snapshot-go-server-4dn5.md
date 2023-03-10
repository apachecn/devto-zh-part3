# volsnap

> 原文：<https://dev.to/didil/volusnapcloud-volume-auto-snapshot-go-server-4dn5>

### VoluSnap —云卷自动快照 Go 服务器

您是否将您的 web 应用部署到**云虚拟机**上，如数字海洋水滴或 Scaleway 云实例？

如果你的应用程序将任何类型的数据保存到磁盘，而你担心由于软件错误、人为错误或恶意行为者(以及其他风险)导致的**数据丢失**，那么你需要一个**备份策略**。

[![](img/42f3f8c9a8b34544d82b814f866deced.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y3NyrMrk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Azea8_CAZQLFwUS-uG7KHXg.jpeg) 

<figcaption>复古数据备份:)</figcaption>

### 云卷备份

大多数云提供商都提供了 ***热备份*** 功能，该功能可以为您的卷数据拍摄快照，而无需在备份过程中停止您的实例(但是存在数据丢失的风险，请仔细阅读该主题并做出明智的决定)。

然而，**自动重复备份**并不总是可用，有时配置选项**不够精细**。在撰写本文时，Digital Ocean 每周进行一次备份，Scaleway 只提供手动备份。

幸运的是，大多数云提供商都提供 **API 访问**。我需要一个自动化备份解决方案来部署我的个人云已经有一段时间了，所以我决定构建一个开源解决方案:

### VoluSnap:用 Go 构建的自托管解决方案

[Volusnap](https://github.com/didil/volusnap) 是用 Go 搭建的 API 服务器。它允许触发云提供商卷的自动重复快照。以下是解释 VoluSnap 工作原理的简化模式:

[![](img/73b46bb3e8b76177e177f72753b0ef28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SmDqPbO_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/891/1%2AbxQvFXfltfySuMSshhCwkg.png) 

<figcaption>API 服务器+规则检查器 Goroutine</figcaption>

基本工作流程是:

*   用户注册
*   用户登录，生成 JWT 令牌
*   用户添加云帐户和相关的云 API 访问令牌
*   用户列表云卷
*   用户为所选卷创建快照规则
*   规则检查器服务定期检查快照规则，通过云提供商 API 触发快照，并将快照元数据保存到数据库

VoluSnap 用途:

*   [gorilla/mux](https://github.com/gorilla/mux) 用于路由
*   [sqlboiler](https://github.com/volatiletech/sqlboiler) 用于将数据持久化到 PostgreSQL
*   [sql-migrate](https://github.com/rubenv/sql-migrate) 用于数据库迁移
*   [蝰蛇](https://github.com/spf13/viper)进行配置
*   [为单元测试和模拟作证](https://github.com/stretchr/testify)

组件:

*   REST API
*   SnapRules 检查器服务

REST API 记录在 [github repo](https://github.com/didil/volusnap#api) 中。目前没有提供客户端代码。

[![](img/97c0bbf600fd3ee8bbb60bbfe8a45270.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2C-jsGlk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/907/1%2ArzQmA63fwMb-2P27E_itQw.png) 

<figcaption>VoluSnap API 文档</figcaption>

目前，REST API 和 SnapRules Checker 组件在同一个 Go 进程中运行。SnapRules Checker 服务目前在一个 goroutine 中运行，但如果需要，将来可以将其拆分到自己的进程中: