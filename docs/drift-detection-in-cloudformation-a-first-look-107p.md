# 云形成中的漂移探测:初步观察

> 原文：<https://dev.to/xiaket/drift-detection-in-cloudformation-a-first-look-107p>

如果你像许多 AWS 专业人员一样关注[‘AWS 的新功能’](https://aws.amazon.com/new/)页面，你一定注意到了[最近宣布的新漂移检测支持](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-cloudformation-now-supports-drift-detection/)。我们已经深入研究了这个新特性，这篇文章是关于我们到目前为止的发现。

漂移检测是 Cloudformation 工具集多年来缺失的许多功能之一。理想情况下，我们应该总是使用 Cloudformation 来发布我们的基础设施变更。然而，实用性是规则，我们经常不得不在云形成之外改变我们的基础设施。当我们这样做时，我们的基础设施的配置偏离了在 Cloudformation 中定义的内容。我们不时需要总结一下哪些事情已经过去了，这样我们才能思考我们应该做些什么。并非所有的漂移都是不好的，有些甚至是意料之中的。例如，为了动态地启用/禁用端点，我们可能已经放置了一个 lambda 函数来调整负载平衡器规则的优先级，这可能会导致漂移。因此，我们应该关注在临时操作期间产生的其他漂移，例如在流量高峰期间引入的自动缩放组的最大大小的变化。

## 用 Python 运行漂移检测

现在这个特性已经可用了，我想写一个 python 脚本来检测我们的堆栈中的漂移，并使用我们的 Jenkins 来定期运行这个脚本，这样我们就可以了解漂移的最新情况。我安装了最新版本的 boto3(>=1.9.44)并查看了 boto3 API:我们需要用一个`DetectStackDrift`调用来触发漂移检测，然后用一个`DescribeStackDriftDetectionStatus`调用来查看结果。因为完成漂移检测需要时间，所以我们需要用一个循环封闭第二个调用，所以它看起来像这样:

```
client = boto3.client("cloudformation")
stack = "your-stack-name"
detection_id = client.detect_stack_drift(StackName=stack)
while True:
    time.sleep(3)
    response = client.describe_stack_drift_detection_status(
        StackDriftDetectionId=detection_id
    )
    if response['DetectionStatus'] == 'DETECTION_IN_PROGRESS':
        continue
    else:
        print(f"Stack `{stack}` has a drift status: {response['StackDriftStatus']}") 
```

一般来说，这个代码片段应该会给出与您在 AWS web 控制台中看到的相同的结果。很简单。

## 来电`DetectStackDrift`

现在让我们后退一步，看看这个`DetectStackDrift` API。除了明显的堆栈名称参数之外，这个调用还可以选择接受堆栈中定义的逻辑记录 id 列表。

我们应该担心堆栈中定义的不支持漂移检测的资源吗？不完全是。默认情况下，如果您在没有这个列表的情况下调用`DetectStackDrift` API，并且您的堆栈碰巧有不支持漂移检测的资源，AWS 将简单地跳过它们。

我们能否找出这个堆栈中所有资源的最后更改时间，也许借助于一个`DescribeStackResources`调用，并跳过对一个堆栈的漂移检测？遗憾的是，答案是否定的。尽管`ListStackResources`和`DescribeStackResources`都返回一个`LastUpdatedTimestamp`字段，但它只记录了它何时被 Cloudformation 更改。当在 Cloudformation 之外更改资源时，此字段不会更新。因此，您不能通过列出/描述堆栈资源调用来缩短`DetectStackDrift`调用。

总之，除非您有一些非常具体的需求，否则我们建议只调用`DetectStackDrift`并将栈名作为唯一的参数，让 AWS 处理其余的事情。

## 用`DescribeStackDriftDetectionStatus`获得漂移状态

如果您仔细查看来自`DescribeStackDriftDetectionStatus`调用的响应，它应该有几个字段，包括:

*   `StackDriftStatus`:堆栈是否漂移。
*   `DetectionStatus`:检测成功还是失败。
*   `DriftedStackResourceCount`:已经漂移的资源数量。

这就是事情变得棘手的地方。在某些情况下，AWS 将无法检测模板中定义的某些资源的状态，在这种情况下，响应将类似于:

```
{
    "StackId": "arn:aws:cloudformation:ap-southeast-2:123456789012:stack/your-stack-name/a61c13dc-e875-11e8-8f0f-000c6c095ac3",
    "StackDriftDetectionId": "b11fdd5e-e875-11e8-8f0f-000c6c095ac3",
    "StackDriftStatus": "IN_SYNC",
    "DetectionStatus": "DETECTION_FAILED",
    "DetectionStatusReason": "Failed to detect drift on resource [SNSTopic]",
    "DriftedStackResourceCount": 0,
    "Timestamp": datetime.datetime(2018, 11, 14, 22, 59, 43, 536000, tzinfo=tzutc()),
} 
```

根据记录，这个逻辑资源 ID `SNSTopic`确实是一个 SNS 主题，它应该有漂移检测支持。从我们可以看到，行为是一致的:如果漂移检测失败了一次，无论你尝试多少次，它都会继续失败。但是，这是不一致的，因为相同的资源类型在一个堆栈中可能会失败，而在另一个堆栈中却不会。

## 其他轻微的打嗝

官方文档确实提到了一些限制。我建议先花点时间读一读。以下是我们观察到的一些其他问题:

### 空虚`PropertyDifferences`

一些资源被报告为`MODIFIED`，没有属性差异。在下面 Cloudformation 的回应中，`ExpectedProperties`和`ActualProperties`完全相同，`PropertyDifferences`为空列表，而`StackResourceDriftStatus`据报道为`MODIFIED`。

```
{  "StackId":  "arn:aws:cloudformation:ap-southeast-2:123456789012:stack/your-stack-name/a61c13dc-e875-11e8-8f0f-000c6c095ac3",  "LogicalResourceId":  "TargetTrackingPolicy",  "PhysicalResourceId":  "some-arn",  "ResourceType":  "AWS::AutoScaling::ScalingPolicy",  "ExpectedProperties":  {  "AutoScalingGroupName":  "your-stack-name-AutoScalingGroup-L3YOV2E9J92N",  "Cooldown":  900,  "EstimatedInstanceWarmup":  300,  "PolicyType":  "TargetTrackingScaling",  "TargetTrackingConfiguration":  {  "PredefinedMetricSpecification":  {  "PredefinedMetricType":  "ASGAverageCPUUtilization"  },  "TargetValue":  40  }  },  "ActualProperties":  {  "AutoScalingGroupName":  "your-stack-name-AutoScalingGroup-L3YOV2E9J92N",  "Cooldown":  900,  "EstimatedInstanceWarmup":  300,  "PolicyType":  "TargetTrackingScaling",  "TargetTrackingConfiguration":  {  "PredefinedMetricSpecification":  {  "PredefinedMetricType":  "ASGAverageCPUUtilization"  },  "TargetValue":  40  }  },  "PropertyDifferences":  [],  "StackResourceDriftStatus":  "MODIFIED",  "Timestamp":  "2018-11-14T22:53:53.090Z"  } 
```

### 展平列表

在下面的`DescribeStackResourceDrifts`响应摘录中，`NotificationTypes`被展平为一个字符串。我不是 100%确定，但我认为发生的事情是我们手动保存了 ASG，这触发了对所有内容的保存并夷平了`NotificationTypes`。这两种配置将以相同的方式工作，我认为这不是真正的改变，这可以由 AWS 内部处理。

```
# ExpectedProperties "NotificationConfigurations": [
    {
        "NotificationTypes": [
            "autoscaling:EC2_INSTANCE_LAUNCH"
        ],
        "TopicARN": "arn:aws:sns:ap-southeast-2:123456789012:your-stack-name-SNSTopic"
    }
]
# ActualProperties "NotificationConfigurations": [
    {
        "NotificationTypes": "autoscaling:EC2_INSTANCE_LAUNCH",
        "TopicARN": "arn:aws:sns:ap-southeast-2:123456789012:your-stack-name-SNSTopic"
    }
] 
```

### 内部类型变化

在下面的例子中，我们调整了安全组入口规则，并将其改回原样。在第二个保存步骤中，AWS 必须将`IpProtocol`保存为一个名称(`tcp`)，但是在模板中，我们将它指定为一个数字(`6`)。根据 RFC，6 号协议只是 TCP，所以这根本不应该是一个漂移。

### 改变列表项的顺序

我的一个同事报告说，对于一个安全组列表，安全组顺序的改变被认为是一种漂移。我还没有见证这个案子。

## 外卖

优点:

*   这肯定会帮助我们更好地维护我们的云形成堆栈。

坏消息:

*   预计会有一些小故障。
*   资源类型支持是有限的(目前)。

丑陋的:

*   一些错误会被忽略(暂时)。

参考资料:

[这一新特性的一般介绍](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html)
[支持漂移检测的资源类型](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift-resource-list.html)
[最新的 boto3 cloudformation API，其中包括新的 API](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/cloudformation.html)