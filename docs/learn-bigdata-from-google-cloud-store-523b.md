# 从谷歌云平台学习大数据。

> 原文：<https://dev.to/saifali40/learn-bigdata-from-google-cloud-store-523b>

是的，我在帖子里加了 ML(机器学习)。我知道有些人会对我为什么在帖子上标记 ML 感到困惑，机器学习基本上需要一些数据来学习。谷歌的大查询有。Google big query 有一个创建机器学习模型的选项，但在这一部分我不会带你去学习机器。

让我们进入正题，想学习大查询(大数据)的人可以使用你的谷歌账户，他们为你提供免费的沙盒选项。

第一步:
[https://console.cloud.google.com/projectcreate](https://console.cloud.google.com/projectcreate)

(您可以选择任何环境，我们现在不打算在云中部署任何东西。如果你感兴趣的话，我也可以写写这个。)

如果你在 GCP 已经有一个项目，你可以跳过这一步。

第二步:
转到大查询([https://console.cloud.google.com/bigquery](https://console.cloud.google.com/bigquery)

[![Dashboard](img/77a427633a36bd99bbe3354a36a93f24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vN-LRTtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6s61j9823kxqr8hisvhl.png)

确保您选择了正确的项目名称。

第三步:
我们需要一些样本数据，让我们看看 Noaa Tsuanmi 的历史上升数据，它有公元前 2000 年的海啸上升数据。记录总数 27057(日期 2019 年 4 月 7 日)。

要使用它，您可以单击+添加数据。[![ADD DATA](img/1c8205d41ae84a544a724d6b47322903.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lorEbRAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9jwkcbj5vx70zequfrz.png)

单击浏览公共数据集。

第四步:

在搜索类型海啸(你可以选择任何公共数据集，但我现在显示的是海啸。)

第五步:
点击查看数据集，它会自动添加到你的数据集中。

现在，确保您可以看到公共数据集
[![public datasets](img/e72e16ee4f9c5a61ae6268035aa0fe44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0XyoDMDy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xyi5gvm2qxii119ahbxa.png)

现在让我们做一些查询。

在这里，我将查询从公元前 2000 年开始的所有运行，并希望看到结果。

[![console](img/caa311d17ff8f913b155575eab0916f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RDtsvxFQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ii9i6e57qfvnzcm6622v.png)

你们可能注意到了，我在大查询中使用 SQL 进行查询，回答是。

我现在要按国家计数。

```
SELECT  country, count(*) counts FROM  `bigquery-public-data.noaa_tsunami.historical_runups` AS runnups
GROUP BY country
ORDER BY counts DESC 
```

[![resutls](img/cbfeabf5baf85367e70d8688b68f72a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AYrcw5rd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ri9f62j6nf890ef1qpg6.png)

你们可能已经注意到我在查询中使用了 AS`SELECT country, count(*) counts FROM`big query-public-data . NOAA _ tsunami . historic _ run ups`AS runnups`将在下一部分解释这一点。

现在您可以自己探索 bigquery 了。

注意:
*)直到上个月我才熟悉 SQL 查询。学习大查询学的，跟着机器学习学的。
*)大查询有很多 SQL 做不到的特性。
*)请在大查询中探索一些新的东西，在添加文章时使用#bigquery，这样我们都可以轻松赶上。

感谢
快乐学习< 3