# AWS DevOps Pro 认证博文系列:CloudWatch

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-cloudwatch-3n5b>

萨夫夫在 [Unsplash](https://unsplash.com/search/photos/cloud-watch) 拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

云观察...

*   是一项允许您收集、跟踪和分析度量 AWS 资源和应用程序的服务。
*   包括:
    *   仪表板-这是一个可定制的网页，可以显示图表，指标，文本和查询结果。DevOps 工程师将使用仪表板来提供系统/环境运行状况的概览视图。
    *   指标允许您可视化您的指标并创建警报。
    *   以下服务(在考试范围内)将指标发布到 CloudWatch。完整列表可在[用户指南](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html)中找到。
        *   亚马逊云锋
        *   CloudWatch 事件和日志
        *   AWS 代码构建
        *   AWS EC2 /自动缩放
        *   AWS ECS
        *   AWS / EFS
        *   英国文学学士（bachelor of english literature）
        *   AWS 室壁运动
        *   AWS Lambda /阶跃函数
        *   aws opsworks
    *   您可以使用 AWS SDK 通过 AWS CLI/API 或应用程序从 EC2 实例发布定制指标。
    *   警报是根据您的指标值或基于该值的数学表达式生成的。
    *   警报的常见用例有:
        *   CPU/内存使用率
        *   负载平衡器延迟
        *   存储吞吐量
        *   账单(超过支出阈值)
    *   警报甚至可以用于停止、终止、重启或恢复实例
        *   警报有三种状态:
        *   正常-指标或表达式在定义的阈值范围内
        *   警报-度量或表达式超出定义的阈值
        *   数据不足-警报刚刚开始，但指标不可用，或者指标没有足够的数据来定义其警报状态
        *   警报只调用持续状态变化的动作。所以你不会一直产生警报，因为你处于状态警报。
    *   日志按日志组聚合，要深入到实际日志，您需要选择一个日志流。
    *   Insights 允许您对日志数据运行类似 SQL 的查询。一个示例查询可能是查看 Lambda 函数最昂贵的请求。
    *   事件是围绕 AWS 资源变化的系统事件的近实时流
    *   规则匹配传入的事件，并将它们路由到目标
    *   目标是处理事件的东西(SNS 主题，Lambda)

指标保持期

*   高分辨率(< 60 秒)为 3 小时。必须激活高分辨率，并产生额外费用。
*   60 秒(1 分钟)的数据点是 15 天
*   300 秒(5 分钟)的数据点是 63 天
*   3600 秒(1 小时)的数据点是 455 秒(15 个月)

其他资源:

*   [CloudWatch 产品页面](https://aws.amazon.com/cloudwatch/)
*   [CloudWatch 常见问题解答](https://aws.amazon.com/cloudwatch/faqs/)
*   [CloudWatch 用户指南](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/index.html)
*   [CloudWatch API](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/index.html)
*   [CloudWatch CLI](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html)

## 为什么？

CloudWatch 使您能够将日志记录和指标聚集到一个集中的位置。然后，您可以执行分析或可视化数据。此外，您可以使用警报和事件来通知您的团队或执行自动反应行动。

## 什么时候？

您应该使用 CloudWatch 代理从 EC2 实例或本地服务器(基于 Linux 或 Windows)收集指标和日志。

如果您只需要从 EC2 实例或本地服务器发布定制指标，那么您应该使用 AWS CLI。

## 如何？

TODO:不幸的是，我发现这些部分占用了我大量的学习时间。我会尝试在我的修改阶段重新审视这一点。

以下是我对这一部分的计划:

*   如何安装 CloudWatch 代理日志以从 EC2 实例中捕获日志，这将基于[本](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)指南。
*   如何发布自定义指标并使用 AWS CLI 生成警报，这将基于[本](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/PublishMetrics.html)指南。

## API 和 CLI 特性和动词

我大胆猜测，你需要熟悉的一个 API/CLI 调用是`put-metric-data` [CLI](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html) [API](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricData.html) ，因为它有直接的实际用途，而不可能通过 AWS 控制台使用。

**特性**

*   警告
*   仪表盘
*   韵律学

**动词(CRUD)**

*   ？？？奇怪的是，创造动词是通过 put
*   描述(警报)/获取(仪表板/指标-数据)/列表(仪表板/指标)
*   put(仪表板/指标-[警报/数据])
*   删除(警报/仪表板)

**离群值**

*   描述-报警-历史记录
*   描述-度量警报
*   禁用-报警-操作
*   启用-报警-操作
*   获取度量统计数据
*   获取度量小部件图像
*   资源的列表标记
*   设置-报警-状态
*   标签资源
*   UNTAG-资源
*   等待

Unsplash path(我用什么术语来获得封面图片):云观察(ikr)

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*