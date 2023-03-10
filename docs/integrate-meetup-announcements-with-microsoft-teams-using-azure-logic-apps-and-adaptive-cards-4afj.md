# 使用 Azure Logic 应用程序和适配卡将 Meetup 公告与微软团队集成

> 原文：<https://dev.to/simonwaight/integrate-meetup-announcements-with-microsoft-teams-using-azure-logic-apps-and-adaptive-cards-4afj>

最近，我发现自己加入了太多的聚会，以至于越来越难跟踪一个团体何时宣布一个新事件。此外，在我的亲密圈子里，我有几个人想与我分享新的活动公告。

因此，我想找到一种方法来跟踪新事件，并确保我自己和其他人可以看到它们，即使我们不是 meetup 组的直接成员。

在这篇文章中，我将看看我们如何使用 Azure Logic Apps 向微软团队发布新的 Meetup 事件信息，使用 Meetup 的 REST API 作为数据源。

**注意:**从 2020 年 6 月起，Meetup 要求希望调用其 API 的客户端进行 OAuth2 认证。我已经写了一篇[单独的文章](https://dev.to/simonwaight/adding-oauth2-support-to-your-meetup-api-custom-connector-for-azure-logic-apps-5a16)，讲述如何给这篇文章中提到的自定义连接器添加 OAuth2 支持。

### 了解 Meetup REST API

Meetup 在[https://www.meetup.com/meetup_api/](https://www.meetup.com/meetup_api/)发布了他们的 API 规范，为了我的目的，我使用了 [V3 事件端点](https://www.meetup.com/meetup_api/docs/:urlname/events/#list)作为我的数据源。

我在使用 API 时遇到了一些挑战，我在最终的 Logic 应用程序中解决了这些挑战。这些挑战是:

*   节流:在您的客户端被节流之前，允许的调用数量相对较少。确切的数字[没有记录](https://www.meetup.com/meetup_api/docs/#limits)，尽管它们在 API 的 HTTP 头中发布回来(见下文)。

[![Throttling Feedback in API](img/ff2da04cee0939cf2d1183363891e3dd.png "Throttling Feedback in API")](https://res.cloudinary.com/practicaldev/image/fetch/s--YfZFgg_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-21_11-03-15.png%3Fw%3D300%26h%3D289)

鉴于 Logic Apps 能够同时运行多个操作，我第一次运行时就受到了限制！为了解决这个问题，我在 Logic 应用程序中使用了[延迟动作](https://docs.microsoft.com/en-us/azure/connectors/connectors-native-delay)来降低我点击 API 的速度。

*   日期格式差异:Meetup API 文档称他们在 API 中接受/发布 [ISO 8601 日期时间](https://en.wikipedia.org/wiki/ISO_8601)值。当尝试使用 Logic Apps 生成的 ISO 8601 本地日期时间时，我遇到了问题，因为 Logic Apps 生成基于 UTC 的日期/时间。API 对此表示不满，因此我只需处理传递给 API 的日期时间值，以匹配预期的格式。调用的时间部分并不重要，所以我主要使用日期部分，并手动添加 T00:00 作为时间。
*   使用 OAuth2 的授权保护:Logic 应用程序的 HTTP 连接器目前不支持 OAuth2，所以我不得不使用 Meetup 的 API key 方法。这种方法不再被 Meetup 官方支持，但是现在对我来说是有效的。为了确保 API 密匙的安全，我将它作为一个秘密保存在密匙库中(请注意，该值以明文形式显示在运行历史中，因此，如果您使用 Logic App，请确保在管理它时[使用安全建议](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-securing-a-logic-app#secure-access-to-logic-app-run-history))。

### [T1】创建我们的数据源](#creating-our-datasource)

保存我们将要跟踪的 meetups 信息的最简单、最便宜的方法是使用 Azure Table Storage ，这是 Azure 中最初的 NoSQL 存储选项！

我找到了下面的模式来举办我正在跟踪的聚会:

*   分区键:城市，即悉尼
*   RowKey: Meetup 的 URL 路径，即 Azure-Sydney-User-Group
*   LastEventId:包含来自 Meetup 的事件 Id 的字符串，即 261477320
*   LastEventTime:包含上次聚会的 ISO 8601 日期/时间的字符串，即 2019-05-30T17:46，稍后将详细介绍
*   TeamChannel:包含发布公告的团队中的渠道名称的字符串，例如悉尼
*   Track: boolean 表示我们是否应该跟踪这个会议

如果我的解释不清楚，下面是一些行在 Explorer 中的样子。时间戳字段是为表存储中的每一行自动创建的。

[![Data Row in Explorer](img/6018efd3349a29c7bc4ec3d4960b4333.png "Data Row in Explorer")](https://res.cloudinary.com/practicaldev/image/fetch/s--sIXg5OxU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-24_08-44-52.png%3Fw%3D1024%26h%3D102)

LastEventTime 字段用于保存几个值-如果在监控窗口内没有新的会议通知，则该字段保存逻辑应用程序运行的最后时间。

我使用了这种方法，因此我们限制了我们检查聚会的时间范围，以避免无限期地选择预先安排好的聚会。

在表存储中导入或创建数据的最简单方法是使用免费的 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ，然后使用 CSV 作为数据源。

### 使用自适应卡

当我想向微软团队发帖时，我希望有一种方式来吸引人们对信息的注意，并为团队用户提供一种与之交互的方式。

最初，我认为只需使用标准消息向团队发帖，并包含 Meetup 活动的 URL 就足够了。结果是这种方法根本不提供任何用户交互——甚至不能点击链接！

感谢 Twitter 上的一些人，我被指向了 [Adaptive Cards](https://adaptivecards.io/) ，它旨在提供一种跨平台以类似卡片的格式呈现丰富内容的一致方式。

我不是设计师，所以我借用了[活动更新示例](https://adaptivecards.io/samples/ActivityUpdate.html)，并根据我的使用情况对其进行了修改，将其包含在我的 Logic 应用程序中，用 Logic 应用程序生成的内容替换了一些字段信息。

[![Adaptive Card Sample](img/3f8d5b917bf70e2097ddaa7d65c3b5b8.png "Adaptive Card Sample")](https://res.cloudinary.com/practicaldev/image/fetch/s--mI_DuPtI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-21_11-49-23.png%3Fw%3D300%26h%3D224)

> ❗ **注意:**你不能在适配卡中嵌入 HTML，所以要确保你放入占位符的文本只是纯文本。

下面是我的适配卡的样子(就是嵌入在 Logic App 里的那个)。

[![My Adaptive Card](img/404c5a093d1b0448c77a59160ba26d0c.png "My Adaptive Card")](https://res.cloudinary.com/practicaldev/image/fetch/s--zppf7njQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-23_12-03-35.png%3Fw%3D1100)

### 发帖入队

在我的场景中，我想加入几个微软团队。其中一个团队只有一个频道，我列出了所有的聚会，所以我“硬编码”了这个频道。对于我的第二个团队，我想根据 Meetup 所在的城市动态选择频道。

在我在 GitHub 上提供的模板中，我只有第二种体验，这样你就可以看到如何在 Logic 应用程序中找到团队通道信息，然后在你稍后呼叫团队时使用这些数据。

您需要能够访问您要发布到的团队–初始连接授权使用您的 Office 365 用户上下文。

> ❗ **注意:**如果你需要找到你想要发布的团队的唯一 ID，你可以使用团队来找到它。右键单击团队并选择“获取团队链接”。在您收到的 URL 中，您会发现一个参数“groupId”——该值是在您的 ARM 模板中使用的团队 Id(见下文！)

[![Team ID](img/09835c104f65fb95844f3635cc52e64e.png "Team ID")](https://res.cloudinary.com/practicaldev/image/fetch/s--OzOTowSz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-23_11-56-03.png%3Fw%3D1100)

### 自己打造！

我已经创建了一个 Azure Resource Manager (ARM)模板，其中包含了将该解决方案部署到您的 Azure 环境所需的所有组件。你可以在 GitHub 上找到它:[https://github.com/sjwaight/MeetupAndTeamsLogicApps](https://github.com/sjwaight/MeetupAndTeamsLogicApps)

在部署 ARM 模板之前，您需要做一些事情。

以下命令使用了 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) ，并假设您有适当的订阅权限来执行这些操作(通常贡献者就足够了)。

#### 1。创建资源组

更改“位置”和“名称”以满足您的需要(如果您愿意，也可以保留它们)。

```
az group create --location westus2 --name meetup-bridge-01 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。创建 Azure 广告服务主体

这将用于从 Logic 应用程序访问密钥库。您必须在 Azure AD 中拥有足够的权限才能执行此操作。

根据需要更改“名称”和“范围”以匹配您的环境。

```
az ad sp create-for-rbac \ 
--name meetupkeyvault \ 
--role reader \ 
--scopes /subscriptions/{YOUR\_SUBSCRIPTION}/resourceGroups/meetup-bridge-01 
```

Enter fullscreen mode Exit fullscreen mode

这将返回类似下面的结果

```
{ 
  "appId": "XXXX9fe0-XXX-XXXX-XXXX-e611592bXXXX", 
  "displayName": "meetupkeyvault", 
  "name": "http://meetupkeyvault", 
  "password": "XXXXXXXXXXXXXXXXX", 
  "tenant": "XXX08c33-XXXX-XXXX-XXXX-48065adeXXXX"
} 
```

Enter fullscreen mode Exit fullscreen mode

确保记下 AppId、密码和租户字段。

#### 3。部署 ARM 模板

您需要提供一些参数来部署 ARM 模板。

*   vaults_meetupkeyvault_name:要创建或使用的密钥库的名称。
*   Storage accounts _ meetuplist _ name:要创建或使用的存储帐户的名称。
*   ad_service_principal:用于访问密钥库的 Azure AD 服务主体的应用程序 ID。
*   deployment_region:模板要部署到的 Azure 区域。这应该与您的资源组的区域相匹配。
*   target_teams_group_id:您要使用的 Microsoft Teams 组的唯一 id。我在上面介绍了如何找到它。
*   keyvault_1_token:clientSecret:为您的 Azure 广告服务主体随机生成的密码。
*   meetup_api_key:您的 Meetup API 密钥将作为秘密存储在密钥库中。

部署模板后，您会发现逻辑应用程序执行失败。这是因为您需要重新授权团队和关键 Vault 连接器。您可以通过在门户中打开连接并遵循该过程来实现这一点。

[![Auth Connection](img/99a1e74fcafc1ac1b1ca483e35d53292.png "Auth Connection")](https://res.cloudinary.com/practicaldev/image/fetch/s--1EqB_zQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/05/2019-05-23_13-33-38.png%3Fw%3D1100)

在使用密钥库连接器之前，请确保您的用户能够修改目标密钥库。如果您使用 ARM 模板创建了密钥库(而不是使用现有的密钥库),那么您的用户一开始就没有密钥库的权限。

一旦你重新授权了连接，你就可以开始工作了，当新的聚会宣布时，你会在团队中得到频繁的更新。

😎