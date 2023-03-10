# 门控释放

> 原文：<https://dev.to/funkysi1701/gated-release-4772>

软件的自动化发布是很棒的，但是我们如何添加反馈元素，以便只有好的发布才能上线。

我使用 Azure DevOps 向谷歌 Play 商店发布我的 android 应用已经有一段时间了。有选择部署到阿尔法，测试或生产轨道，甚至设置%的用户为目标。要获得完整的选项，请查看 Azure DevOps 的 Google Play [扩展](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play)。

我的版本从发布到生产轨道上的 10%的用户开始，我的下一步是利用增加部署选项来增加这个百分比，您可以根据需要增加这些额外的步骤，直到您达到 100%的用户。

[![Image](img/9581f345e2882c3aed4515d4f6156e71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yvi5QgGl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2019/04/image.png%3Ffit%3D662%252C116%26ssl%3D1)

现在，如果您现在运行这个版本，它将一个接一个地运行每个步骤。现在，您当然可以在您的管道中添加前置或后置审批，但这只是为您的发布添加了一个手动依赖项。无论是谁批准，都需要在批准前检查工作是否正常，或者更糟的是不管不顾就批准。

Azure DevOps 有[门控发布](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/deploy-using-approvals?view=azure-devops)的概念，它允许你在发布发生之前或之后添加自动检查。这些自动检查可以是以下任何一种:

*   蔚蓝色的函数
*   Rest API 调用
*   Azure 监视器警报
*   查询工作项
*   安全性和合规性评估

我们将利用 Azure Monitor 警报，根据您的应用洞察数据创建一个警报，并且仅在没有检测到故障的情况下继续推广。

在 Azure 门户中打开您的应用洞察资源，查看警报。单击添加新警报规则。

[![Image](img/e559437aa0e01c646ea86866295c56c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z4FFPMC5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2019/04/image-1.png%3Ffit%3D662%252C552%26ssl%3D1)

在 resource 中选择 application insights 资源，在 Condition choose a Condition to check 中，我选择了 Failed Requests，因此每次在我的 API 中注册失败时，我都可以停止部署。您想要使用的确切标准完全由您决定。

创建一个行动组，我刚刚设置了我的提醒发送电子邮件给自己，但还有其他提醒行动，你可能想尝试。为您的警报提供名称和描述，然后单击保存。

现在我们需要做的就是让 Azure DevOps 利用这个警报。在发布管道中，选择第二步的预部署条件，并打开 Gates 部分。

[![Image](img/f282d688f0d6941d40bed8356c4fa836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CPhRzFak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2019/04/image-2.png%3Ffit%3D662%252C498%26ssl%3D1)

选择一个合适的时间来评估，我已经用了 12 或 24 小时，所以如果有问题，有时间让它被注意到。选择任务的版本 1(我无法让它与版本 0 一起工作)

现在选择你的 Azure 订阅和资源组，其余设置保持不变。现在，您的部署将停止并分析应用程序洞察任何失败的请求，如果发现任何失败的请求，它将停止。

[![Image](img/8ca49c4c4c8622196bbbb175f2cb4960.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BX-Hy0JJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2019/04/image-3.png%3Ffit%3D662%252C88%26ssl%3D1)

我仍然在测试这一点，但这将需要几天的时间来弄清楚这是否是我想要的，因为涉及到大量的时间尺度。我觉得这将是手动批准发布步骤的一个改进。