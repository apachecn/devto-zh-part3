# 汉堡 2019 年无服务器日-第 1 天

> 原文：<https://dev.to/dvddpl/serverless-days-hamburg-2019---day-1-workshops-195o>

本周，我有机会参加了在汉堡标志性的[圣保利体育场](https://www.fcstpauli.com/verein/millerntor-stadion/)举行的无服务器日活动。

[![Sankt Pauli Museum](img/f9e72511e237fd55da84c62f4ca9a9bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CzWYM_YW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g5t2h5frgw496ipre4rw.jpg) 
第一天是实践工作坊。有许多可用的途径，我选择了两个关于 AWS 安全性的研讨会。
这些是我做的一些笔记。希望你能找到一些有趣的小技巧。

## 如何保护你的 AWS 账户——Dennis Taub(code centric)

第一部分是关于设置所有必要的安全和监控您的帐户:

CloudTrail 是一项 AWS 服务，提供您的 AWS 帐户活动的事件历史。AWS API ( UI-console 或 cli)上发生的一切都将被注册。

做什么:

*   激活所有区域的云轨迹
*   把所有东西都写到 S3 桶里(但是保存日志超过 90 天——在冰川上或者备份到某个地方的云上),以便取证。
*   确保没有人能够访问这些日志，当然也没有人能够*删除*桶和日志。(最终加密内容)
*   监控铲斗本身正在发生的情况(如果有人试图对其进行调节，您现在就想这么做)
*   使用 Cloudwatch 事件在特定操作的情况下生成警报，例如:
    *   有人试图删除或读取数据
    *   策略被给出或删除
    *   配置已更改
    *   资源是在其他地区创造或使用的
    *   对于更具体的东西(比如被探测的服务器实例或暴力攻击),使用 *GuardDuty* 来检查日志中的模式
*   在 AWS SNS(简单通知服务)中创建一个主题并订阅它，以便在 CloudWatch 上发生警报时立即得到通知，并能够采取行动(例如从导致警报的用户/服务中删除所有角色)

第二部分是关于授予用户或帐户临时访问资源的角色——比如测试员或审计员。这可以通过动作 AssumeRole 和 STS 来实现，STS 是一种授予临时令牌的服务，这些令牌可用于承担特定的角色。

### 备注、有趣的引语和进一步阅读的提示

[![Swiss Cheese Security Model](img/4a888a0d9c26cb46a80c0f3e1129a324.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JUWtvf13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/345vgev1nddp531q7jxp.png)

*   **瑞士奶酪安全**:每一层安全都会有漏洞。如果对齐，它们可能会导致事故。
*   **深度安全**
*   **阻止-检测-通知-遏制**
*   尝试将监控部分(从 S3 bucket 到 SNS)与用 Cloudtrail 监控的部分放在不同的帐户上。
*   始终在资源和角色上配置策略，以防止数据丢失:(您不能将角色分配给 cloud trail——在监控桶的情况下，在桶上创建策略)。
*   请记住，拒绝会否决允许(在策略和角色之间的配置冲突的情况下)
*   **最小特权原则**:不要给出开放的政策，期望以后会受到限制。这永远不会发生..

#### 为什么会有人背信弃义？

窃取数据和文档进行诈骗和间谍，删除数据制造伤害，比特币挖矿。

#### 如果您的账户只使用 EU-西一号，为什么您需要监控所有地区？

因为你肯定想知道是否有人正在其他地区做一些事情(比如在新加坡产生 EC2 实例)

### GMAIL 专业版提示

每当你向 pubsub 服务或时事通讯等提供服务时，你可以在你的电子邮件地址中添加一些单词，这样你就可以在 Gmail
中添加一个过滤器，这样如果你的电子邮件是[jdoe@gmail.com](mailto:jdoe@gmail.com)你就可以输入[jdoe+spamming_newsletter@gmail.com](mailto:jdoe+spamming_newsletter@gmail.com)，然后通过*垃圾邮件 _ 时事通讯*过滤你的电子邮件

## T2】

## 用 AWS 构建安全的无服务器应用 Lambda - Alex Casalboni (AWS)

第二次研讨会相当激烈。在简短而有趣地介绍了 Lambda 权限模型和一些关于执行与功能策略的见解后，我们得到了一大堆取自[https://github . com/AWS-samples/AWS-server less-security-workshop](https://github.com/aws-samples/aws-serverless-security-workshop)的材料，我们基本上浏览了所有内容(嗯，大部分是..)来重建 app。

我必须说，这种研讨会并不是我真正喜欢的类型，因为我更喜欢在不同的环境下独自修改代码，或者与他人一起编程，当我在研讨会上时，我更喜欢更积极的讨论或正面的课程。
但这只是我的学习方式，其他人也很喜欢——我设法学到了很多，并与 Alex 和他的两个助手讨论了许多关于 Cognito 和 Scopes 的细节。

### 备注和提示:

*   不要纠结于你在内存上花了多少钱，因为内存分配少的内存会花更长的时间，你最终可能会花同样多的钱(但是服务更慢)
*   不要在权限策略中使用星号:你甚至可以/应该添加 linter 来阻止它的使用
*   如果你使用 Cloudfront——栈的部署要花更长的时间，试着把栈描述和 Cloudfront 描述分开，这样如果 Cloudfront 栈失败了，至少不是所有东西都被回滚。它最终不会加快速度，但会避免不必要的回滚

* * *

明天我会告诉你关于第二天的事情——那时进行了很多会谈！:-)