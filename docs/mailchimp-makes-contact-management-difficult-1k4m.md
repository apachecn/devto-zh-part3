# Mailchimp 使联系人管理变得困难

> 原文：<https://dev.to/brylie/mailchimp-makes-contact-management-difficult-1k4m>

TLDR: Mailchimp 应该提供一个统一的“联系人管理器”来帮助用户有效地管理他们的联系人列表。这将有助于减少浪费，并尊重收件人的通信偏好。

## 背景

在帮助我们的营销团队管理联系人时，我了解到在 Mailchimp 所谓的“受众”中，我们有大约 45% - 55%的重复记录。起初，我担心营销团队的数据实践，但后来我意识到部分问题与 Mailchimp 接口有关。特别是， **Mailchimp 似乎不允许客户管理统一的联系人列表**。

## 当前设计

Mailchimp 仪表板菜单由以下顶级部分组成:

[![Mailchimp dashboard menu](img/fb759331a41fd938a5c7637660640b7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VIGUGQli--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/etkxfb5mnpxko97c0dgd.png)

值得注意的是，**受众**链接是单数，意味着只有一个核心受众，可能被分成重叠的部分(我的解释)。

从市场营销的角度来看，拥有细分的受众很容易理解。从概念上来说，*受众是所有对你的产品感兴趣的人*。然后，细分将是被分组到各种人口统计数据中的观众成员，这对于定制的通信是有用的。

## 设计含义

实际上， **Mailchimp 受众是不同的联系人列表，允许多个受众中存在重复的联系人**。

联系人有时可能选择退出一个“受众”，但这不会影响他们在另一个受众中的通信偏好。当联系人在选择退出后收到通信时，这可能会导致挫折感。它还会降低公司的声誉，尤其是当用户举报公司的通信是垃圾邮件时。

同样，您的公司将为跨受众的重复联系人付费，就像他们是不同的联系人一样。对于较小的联系人列表来说，这可能并不明显，但仍然是一种浪费(就像你不在家时让灯亮着)。

## 冲击

在我们的案例中，我们在多达五个受众中有相同的联系人。光是支付重复联系的费用，一个月就要加起来几百欧元。

在归档和合并来自我们营销团队创建的多个重复“受众”的联系人后，我们的联系人数量减少了将近一半！

## 财务利益冲突

每当我们使用根据资源使用情况收费的服务公司(无论是 AWS、Mailchimp 等)时，该公司都有财务激励来鼓励相关资源的低效或过度使用。

在这种情况下，如果 Mailchimp 将统一联系人管理和去杜平化整合到其平台中，它将损失相当大一部分经常性收入。也就是说，Mailchimp 不会在未来的某个时候提供有效的联系人管理，这并不是一个预先确定的结论。

## [不怨天尤人](#not-placing-blame)

我不认为这里的错误完全在于营销团队或 Mailchimp，但 Mailchimp 联系人管理的不足和营销团队做出的选择加在一起，形成了昂贵的每月账单。

## 结论

我并不是说 Mailchimp 是一个糟糕的演员——远非如此，因为他们提供了用户友好、诚实的服务。

我只是认为 Mailchimp 的联系人管理需要一些重大的改进。

* * *

[![Creative Commons License](img/e59cd93fa004ceb14ed402ec095ed201.png)](http://creativecommons.org/licenses/by/4.0/) 
本作品获得[知识共享署名 4.0 国际许可](http://creativecommons.org/licenses/by/4.0/)的许可。