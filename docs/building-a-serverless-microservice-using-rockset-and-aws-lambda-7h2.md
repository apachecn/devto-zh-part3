# 使用 rocket 和 aws lambda 构建无服务器微服务

> 原文：<https://dev.to/rocksetcloud/building-a-serverless-microservice-using-rockset-and-aws-lambda-7h2>

*作者[梁家杰](https://www.linkedin.com/in/kevleong/)T3】*

Rockset 是一个无服务器的搜索和分析引擎，可以轻松开发数据驱动的应用程序。这个[视频演示](https://www.youtube.com/watch?v=KBb10Ttxjcc)展示了 Rockset 的一个例子。在这个练习中，我们将构建一个无服务器的微服务来发现 Twitter 上被提及次数最多的股票符号。

[![e59e6e97b5644ea87b8522a8d0295470](img/ac6038ed141d1680f86142de44fe45bf.png)](https://www.youtube.com/watch?v=KBb10Ttxjcc&playsinline=1)

### **摄取**

我们的 Twitter 流来自亚马逊 Kinesis，并不断被摄入 Rockset。从 Rockset 控制台设置 Rockset 和 Kinesis 之间的[实时集成是一个简单的过程。参考我们的](https://docs.rockset.com/amazon-kinesis/)[分步指南](https://rockset.com/blog/live-dashboard-on-streaming-data-using-kinesis/)了解更多细节，包括设置 Twitter Kinesis 流的信息。

我们还想将 Twitter 上的股票提及与来自纳斯达克(Nasdaq)的股票信息结合起来。这个信息[来自亚马逊 S3](https://docs.rockset.com/amazon-s3/) 的一个文件，并被吸收到第二个 Rockset 集合中。

[![demo outline](img/74cf0f7350c7804dfe517eb64b3b71d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wD59oNwV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/4pMP9erohiOgUWIiGECge0/133b5a832b286c65d66803b1fb091a7e/demo_outline.png)

### **查询**

Rockset 自动推断出`twitter-firehose`集合中 Twitter JSON 数据的模式。我们没有对数据执行任何转换，但是我们可以立即对其运行 SQL 查询。检查我们的 SQL 查询的结果，注意 Twitter 数据是如何组织在多层的[嵌套](https://rockset.com/blog/running-sql-on-nested-json/)和数组中的。

[![Screen Shot 2019-01-04 at 11.22.16 AM](img/6ebfa6f1b8465b1cbf0c7980d064613d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hKXMXIZ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/z5UwbN4ZWKCuuMOCcc8yA/15b16362e674bce41c8706b59d4f9936/Screen_Shot_2019-01-04_at_11.22.16_AM.png)

在我们的例子中，我们特别关注包含股票提及的 tweets，这是在`entities`字段的`symbols`数组下找到的。我们逐渐探索数据并构建我们的 SQL 查询，将 tweet 数据与`tickers`集合中的纳斯达克公司信息连接起来，返回我们数据集中最受欢迎的股票以及每只股票的一些描述性信息。