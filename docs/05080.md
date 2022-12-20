# 使用 Athena 时，请小心意外的 S3 成本。

> 原文：<https://dev.to/cloudforecast/watch-out-for-unexpected-s3-cost-when-using-athena-5hdm>

*最初发表于[cloudforecast.io/blog](https://cloudforecast.io/blog/unexpected-s3-cost-when-using-athena/)T3】*

继我的上一篇博客文章([在 Athena 上使用 Parquet 以在 AWS 上省钱](https://cloudforecast.io/blog/using-parquet-on-athena-to-save-money-on-aws/))之后，我想分享关于 Athena 的另一个想法，特别是 Athena 如何使用 S3 存储桶来存储查询结果。

在第一次使用 Athena 时，AWS 将自动创建一个新的存储桶来存储查询结果(存储桶名称`aws-athena-query-results-<ACCOUNTID>-<REGION>`)。Athena 将存储一个原始结果文件(`QueryId.csv`)和一个元数据文件(`QueryId.csv.metadata`)。

通过使用`QueryId`存储数据，它允许您访问先前查询的结果，而无需重新运行它们(因为您不需要重新扫描数据，所以节省了您的资金)。

但是，您是铲斗的所有者，因此要对铲斗上的存储负责，以下是为什么它会花费您很多钱的几个原因:

所有的查询都被存储了！所有人！

雅典娜将每一个查询结果存储在桶中。查询数据只会不断积累，成本越来越高。

**#2 您的数据可能被压缩，但结果不是这样**

S3 正在将结果存储在原始 CSV 中。您的数据可能被压缩(GZIP，爽快，...)但结果将是原始的 CSV 格式。例如，我使用 Parquet 在一个 84M 的数据集上运行了一个*意外的* `SELECT * FROM flights.parquet_snappy_data`，在 S3 上生成了一个 **977MB 的**文件。

**这个怎么修？**

*其实挺容易的*。如果(且仅当)您不打算重用旧的查询结果，请确保使用转换或到期操作在您的 bucket 上设置[生命周期](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)。例如，您可以在 1 或 7 天后删除查询结果。在 CloudForecast，我们实际上不会持久化`QueryIds`,因为它对我们没有用，所以我们会在 1 天后使 S3 文件过期。

*如果您有任何问题，请通过[Francois @ cloud forecast . io](//mailto:francois@cloudforecast.io)或 Twitter: [@francoislagier](https://twitter.com/francoislagier) 联系我们。还有，跟随我们的旅程 [@cloudforecast](https://twitter.com/cloudforecast)*