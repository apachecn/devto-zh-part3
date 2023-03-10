# 监控 EC2 网络利用率

> 原文：<https://dev.to/andreaswittig/monitoring-ec2-network-utilization-2k31>

您是否在监控 EC2 实例的网络利用率？为什么不呢？网络是限制工作负载最大吞吐量的稀有资源之一:

1.  中央处理器
2.  记忆
3.  网络
4.  唱片
5.  国家政治保卫局。参见 OGPU

在过去的 12 个月里，我已经调试了许多基础设施中的性能问题。在大多数场景中，EC2 或 RDS 实例的网络能力是造成问题的瓶颈。这就是为什么我想与您分享如何监控 EC2 实例的网络利用率。

为了监控 EC2 实例的网络利用率，我们需要解决两个挑战。

## 挑战 1:我的 EC2 实例的网络性能如何？

为了能够监控 EC2 实例的网络利用率，您需要能够回答以下问题。您的 EC2 实例的基线和最大网络吞吐量是多少？不幸的是，对于大多数实例类型，AWS 不提供关于网络性能的准确信息。例如，AWS 承诺`t2.xlarge`实例的`Moderate`网络性能或`m5.large`实例的`Up to 10 Gbps`。

所提供的信息不令人满意。这就是我运行网络性能基准测试并在 [EC2 网络性能备忘单](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)上发布结果的原因。结果是惊人的。

一个`m5.large`实例仅在几分钟内提供 10.04 Gbit/s 的速度。之后，m5.large 实例的基线网络性能约为 0.74 Gbit/s，其他实例类型的结果看起来相似。

[EC2 网络性能备忘单](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)为您提供了对 EC2 实例的基线和最大网络吞吐量的估计，允许您定义监控阈值。

很好，我们已经解决了挑战 1。

## 挑战#2:如何组合多个 CloudWatch 指标？

每个 EC2 实例向 CloudWatch 报告各种指标。指标`NetworkIn`和`NetworkOut`收集实例在所有网络接口上接收的字节数。但是，要计算 EC2 实例的网络利用率，您需要将这两个指标相加。

选择以下选项之一来创建 CloudWatch 警报，以监控 EC2 实例的总网络利用率:

1.  使用 AWS 管理控制台手动创建 CloudWatch 警报。
2.  使用 CloudFormation 以基础设施为代码创建 CloudWatch 警报。
3.  使用 marbot 的跳跃启动来创建 CloudWatch 警报。

### AWS 管理控制台

登录 AWS 管理控制台，进入 [CloudWatch](https://console.aws.amazon.com/cloudwatch/) 。从子导航中选择`Alarms`并点击`Create Alarm`按钮。将出现如下屏幕截图所示的向导。点击`Select metric`按钮。

[![Step 1: Creating CloudWatch Alarm monitoring Network Utilization](img/6144dca7ea9c53b253ab380c78968156.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fhaf_ypg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marbot.img/2019/04/network-utilization-01.png)

搜索 EC2 实例的`NetworkIn`和`NetworkOut`指标，并选择它们。完成后，选择`Graphed metrics`选项卡。

1.  点击`Add a math expression`。
2.  键入 id `out`作为`NetworkOut`度量，键入`in`作为`NetworkIn`度量。
3.  输入表达式`(in+out)/300/1000/1000/1000*8`。

让我快速解释一下数学表达式`(in+out)/300/1000/1000/1000*8`:

*   把`in`和`out`加起来。
*   除以`300`将 5 分钟转换为 1 秒。
*   除以`1000/1000/1000*8`转换成以 Gbit 为单位的字节。

在点击`Select metric`按钮之前，确保您只选择了数学表达式。

[![Step 2: Creating CloudWatch Alarm monitoring Network Utilization](img/508da127a724a43d1c6eb6591fcda10a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bSQSiJ8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marbot.img/2019/04/network-utilization-02.png)

最后，设置闹钟。

1.  键入名称和描述。
2.  定义阈值。例如， [EC2 网络性能备忘单](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)中列出的基线网络性能的 80%。
3.  为避免短期网络利用率峰值报警，请配置`8 out of 12 datapoints`。也就是说一小时内有 45 分钟。

点击`Create Alarm`按钮。

[![Step 3: Creating CloudWatch Alarm monitoring Network Utilization](img/f23c25f9df80a17b3dba2e97c219be0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ljpZNVqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marbot.img/2019/04/network-utilization-03.png)

好了，您已经设置了一个 CloudWatch 警报来监控 EC2 实例的网络利用率。

您可以借助 CloudFormation 以自动化的方式创建 CloudWatch 警报，而不是手动完成这个过程。

### 云的形成

下面的代码片段显示了一个 CloudFormation 模板，它设置了一个 CloudWatch 警报来监视 EC2 实例的网络利用率。

你需要修改`Threshold`。我建议使用 [EC2 网络性能备忘单](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)中列出的 80%的网络基线性能。

```
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  Topic:
    Type: String
  InstanceId:
    Type: String
Resources:
  NetworkUtilizationTooHighAlarm:
    Type: 'AWS::CloudWatch::Alarm'
    Properties:
      AlarmDescription: 'EC2 High Network Utilization'
      Metrics:
      - Id: in
        Label: NetworkIn
        MetricStat:
          Metric:
            Namespace: 'AWS/EC2'
            MetricName: NetworkIn
            Dimensions:
            - Name: InstanceId
              Value: !Ref InstanceId
          Period: 300
          Stat: Sum
          Unit: Bytes
        ReturnData: false
      - Id: out
        Label: NetworkOut
        MetricStat:
          Metric:
            Namespace: 'AWS/EC2'
            MetricName: NetworkOut
            Dimensions:
            - Name: InstanceId
              Value: !Ref InstanceId
          Period: 300
          Stat: Sum
          Unit: Bytes
        ReturnData: false
      - Id: total
        Label: 'NetworkTotal'
        Expression: '(in+out)/300/1000/1000/1000*8' # Gbit/s
        ReturnData: true
      ComparisonOperator: GreaterThanThreshold
      EvaluationPeriods: 12
      DatapointsToAlarm: 8
      Threshold: '0.048' # Gbit/s
      AlarmActions:
      - !Ref Topic
      OKActions:
      - !Ref Topic
      TreatMissingData: notBreaching 
```

您是否正在寻找一种更简单的方法来监控 EC2 实例的网络利用率？

### 马博跳跃开始

我们的聊天机器人 *marbot* 在您的 DevOps 团队成员中升级警报。幸运的是， *marbot* 提供了内置的*跳转启动*，这简化了为您的云资源创建 CloudWatch 警报。EC2 实例的*跳转起点*也设置了对网络利用率的监控。

1.  将 [marbot](https://marbot.io) 添加到您的空闲工作区。
2.  邀请 *marbot* 加入频道。
3.  遵循安装说明。
4.  请求 marbot 帮助监控您的 EC2 实例:`@marbot Help me to monitor my EC2 instance.`
5.  选择`EC2 instance`或`EC2 instances`作为监控目标，按照*跳转启动*向导进行操作，如下图所示。

[![marbot Jump Start](img/f6b29dc6cfbceebfca70e7d63fca7b29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wc-jmM3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marbot.img/2019/04/marbot-jump-start.png)

再简单不过了！

## 总结

监控 EC2 实例的网络利用率是必不可少的，因为网络是一种有限的资源。实例类型会影响最大和基线性能。您的 EC2 实例可能无法在超过 5 到 30 分钟的时间内提供最大的网络性能。因此，使用基线性能来定义警报阈值。使用 [EC2 网络性能备忘单](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)来评估您的 EC2 实例的网络性能。