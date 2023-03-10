# 微软团队 Webhook 集成

> 原文：<https://dev.to/harshadranganathan/microsoft-teams-webhook-integration-4m3l>

最初发表于[rharshad.com](https://rharshad.com/microsoft-teams-webhook-integration/)

微软团队的`Incoming webhook`连接器允许你发布消息到团队频道。

您甚至可以向内容添加操作，以便用户可以在频道内完成任务。

## 给通道添加 Webhook 连接器

在 Microsoft Teams 中，选择频道列表中频道名称旁边的`More options (⋯)`按钮，然后选择连接器。

[![](img/dae388fa48b6be2b3d9fba84b73b69d1.png)](https://rharshad.com/assets/img/2018/05/webhook-connector.png)

将`Incoming webhook`连接器添加到通道中。

现在选择`Incoming Webhook`连接器旁边的`configure`按钮。

[![](img/850eead44db12aed8d153a8c8442bc45.png)](https://rharshad.com/assets/img/2018/05/incoming-webhook.png)

为连接器提供名称并上传图像。现在选择`Create`按钮。

复制 webhook URL 供以后参考。

我们现在已经为频道设置了 webhook。

## 向 Webhook 发送消息

要向 webhook 连接器发送消息，我们必须将其作为 JSON 有效负载发送。请参考[信息卡模板](https://docs.microsoft.com/en-us/outlook/actionable-messages/message-card-reference)了解有效载荷的结构。

注意:[适配卡](https://docs.microsoft.com/en-us/outlook/actionable-messages/adaptive-card)是新团队开发的推荐卡类型。它们是微软产品中新的交叉产品规范，包括机器人、Cortana、Outlook 和 Windows。但是，在团队连接器中还不支持它们。

你可以在[卡牌游乐场](https://messagecardplayground.azurewebsites.net/)试验你的卡牌设计。

我们将发送下面的样本有效载荷到连接器，并检查它是否工作。