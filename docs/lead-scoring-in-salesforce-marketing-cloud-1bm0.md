# Salesforce 营销云中的销售线索评分

> 原文：<https://dev.to/girikon/lead-scoring-in-salesforce-marketing-cloud-1bm0>

Girikon 的 **[Salesforce 咨询服务](https://www.girikon.com.au/salesforce-consultant/)** 团队获得了 Pardot 和 Salesforce 营销云的认证并拥有丰富的经验。他们对这两种自动化营销解决方案都有深入的了解，并有许多见解可以分享。

Pardot 和 Salesforce Marketing Cloud 都是营销自动化解决方案，其中 Pardot 可视为一种工具，Salesforce Marketing Cloud 可视为营销人员的综合工具包。Pardot 和 Salesforce Marketing Cloud 都允许通过其工具/工具包进行一定程度的社交媒体共享，并且都允许用户创建自定义电子邮件活动。

尽管有这些相似之处，Pardot 和 **[Salesforce 营销云](https://www.girikon.com/salesforce-marketing-cloud/)** 仍有一些不同之处，包括销售线索评分模型。不包括销售线索评分模型，但是 Pardot 具有默认评分功能和模型，允许对各种潜在客户活动进行评分。在这篇博客中，我们的 Salesforce 顾问将努力向用户展示如何根据[_Sent]，[_ Open]【T4][_ Click]数据视图从电子邮件中生成销售线索评分。

**来自[_sent]，[_open] & [_click]的数据通过查询中的 join 操作保存到数据扩展‘DE _ 1’:**

简单地执行查询，并根据您的 JobID 将这些数据视图连接到一个单独的数据扩展中。

```
 Ex: 
```

```
 from [\_sent] as a full JOIN [\_open] as b on a.[subscriberkey]= b.[subscriberkey] full join [\_click] as c on b.[subscriberkey] = c.[subscriberkey] Where a.[JobId] = < JobID >. 
```

**供大家参考**

:join 语法:[https://www . w3schools . com/SQL/SQL \ _ join . ASP .[！【数据】(https://www . giri kon . com/WP-content/uploads/2019/05/Data-1024 x386 . png)【https://www . giri kon . com/WP-content/uploads/2019/05/Data . png)](https://www.w3schools.com/sql/sql%5C_join.asp.%5B!%5BData%5D(https://www.girikon.com/wp-content/uploads/2019/05/data-1024x386.png)%5D(https://www.girikon.com/wp-content/uploads/2019/05/data.png))

**创建一个带有默认分值的数据扩展，并保存在数据扩展‘DE _ 2’:**

使用导入向导导入包含订户密钥和初始分数的文件，并将这些记录保存在单独的数据扩展中。

**注意:**创建导入文件时，至少将几个 SubscriberKey 与' DE_1 '匹配。

[![data](img/90a701ea09439a79f9115750c2d01a9e.png)](https://www.girikon.com/wp-content/uploads/2019/05/data1.png)

**连接两个数据(DE_1 & DE_2)扩展并保存到第三个数据扩展(DE_3):**

在查询中使用 case 语句增加或减少您的初始分数，并将其保存在目标数据扩展的“更新分数”字段中，我在下面的图(3.2)中提到过。同时连接这两个数据扩展，并使用基于 SubscriberKey 的查询将其保存到第三个数据扩展。

```
Ex: CASE WHEN a.[< Column name >] IS NOT NULL THEN [Initial Score] + <num>
          ELSE [Initial Score]
          END AS [Updated Score] 
For reference: Case Statement : https://www.w3schools.com/sql/sql_case.asp.
</num> 
```

**创建一个自动化并运行以下活动:**

[![data](img/e80d1e3d473368a42e79534823b74fb2.png)](https://www.girikon.com/wp-content/uploads/2019/05/data-2.png)

**在 Automation 中运行这些活动后，您的记录将在 Data Extension 中以更新后的分数进行更新:**

[![data](img/a1e54c8e93f547d270510cc6fa029b35.png)](https://www.girikon.com/wp-content/uploads/2019/05/data-3.png)

Girikon 的 Salesforce 顾问提供了一些他们在 Salesforce 营销云的销售线索评分方面的经验首先出现在 [Salesforce 咨询公司|美国|亚利桑那州凤凰城](https://www.girikon.com/)。