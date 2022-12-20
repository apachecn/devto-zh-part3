# 使用 Amazon Kinesis 和 Rockset 构建流数据的实时仪表盘

> 原文：<https://dev.to/rocksetcloud/building-a-live-dashboard-on-streaming-data-using-amazon-kinesis-and-rockset-5gai>

*作者为[哈尼什·雷迪·波德都托里](https://www.linkedin.com/in/haneeshreddy/)T3】*

在我们生活的世界中，各种系统——社交网络、监控、股票交易所、网站、物联网设备——都在以事件的形式不断产生大量数据。可以在记录级别或滑动时间窗口对这些事件流执行各种各样的分析，如聚合、过滤或采样。在这篇博客中，我将展示 Rockset 如何服务于一个实时仪表板，该仪表板对从亚马逊 Kinesis 流摄取到 Rockset 的实时 Twitter 数据进行表面分析。

### **设置变身流**

下面的 Python 代码片段展示了如何以编程方式创建 Kinesis 流。这也可以通过 AWS 控制台或 AWS CLI 来实现。