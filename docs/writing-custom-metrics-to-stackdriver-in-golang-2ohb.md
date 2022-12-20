# 在 Golang 中将自定义指标写入 Stackdriver

> 原文：<https://dev.to/tonymet/writing-custom-metrics-to-stackdriver-in-golang-2ohb>

仪器是任何应用的关键部分。除了 cpu、堆、空闲磁盘等系统计数器之外，创建应用程序级指标以确保更接近客户体验地测量运行状况也很重要。

示例指标可以是用户注册、密码更改、简档更改、
等。如果您在这些指标中看到一个主要的峰值或谷值，那么可能会出现一个更大的问题
。

对于这个例子，需要一个定制的指标，并且没有基础设施来收集它(例如 collectd)。Golang 可以方便地创建一个
易于安装的守护进程来执行测量，并定期
将数据收集到 stackdriver 中。

stackdriver 中定制指标的总体流程如下:

1.  使用 stackdriver 指标描述符 API 创建自定义指标——这将为指标分配数据类型、维度、名称和资源类别。
2.  将指标值推送到指标，记录其值、资源和附加维度。

在下面的例子中，我们想要测量由备份代理上传的
文件的数量。文件的数量由一个
日志文件中的行表示，我们用它作为我们的指示器(见`Line Counter`和`WatchFile`)。
通过每 5 秒向 stackdriver 发送一次行数，我们能够创建一个
控制面板，并提醒监控备份活动。

## 1。创建度量描述符

对于您的第一个指标，最简单的方法是使用 API explorer，它将自动完成定义。随着项目的成熟，通过`gcloud`调用`mectricDescriptors.create`

1.  访问[探索者](https://cloud.google.com/monitoring/api/ref_v3/rest/v3/projects.metricDescriptors/create?apix_params=%7B%22name%22:%22projects/%5BPROJECT_ID%5D%22,%22resource%22:%7B%22name%22:%22%22,%22description%22:%22Daily%20sales%20records%20from%20all%20branch%20stores.%22,%22displayName%22:%22daily%20sales%22,%22type%22:%22custom.googleapis.com/stores/daily_sales%22,%22metricKind%22:%22GAUGE%22,%22valueType%22:%22DOUBLE%22,%22unit%22:%22%7BUSD%7D%22,%22labels%22:%5B%7B%22key%22:%22store_id%22,%22valueType%22:%22STRING%22,%22description%22:%22The%20ID%20of%20the%20store.%22%7D%5D%7D%7D)
2.  名称:备份计数
3.  值类型:整数
4.  移除标签。我们将使用 writeMetric 调用分配相关资源

点击此处阅读完整文章...