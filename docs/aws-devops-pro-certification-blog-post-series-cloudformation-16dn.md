# AWS DevOps Pro 认证博文系列:云形成

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-cloudformation-16dn>

Stephanie Klepacki 在 Unsplash 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

CloudFormation 是一种模板语言，可以用 JSON 或 YAML 来表达。这是一个属于这个领域的基础设施代码(IaC)类别的工具。

您需要了解的核心概念是:

*   栈——模板的实例化
*   模板由以下核心元素组成
    *   `Parameters` -用户配置选项
    *   `Mapping` - Hashes(键/值对数组)，允许您应用逻辑，即根据区域选择正确的 AMI。
    *   `Resources` -我们将使用 CloudFormation 提供的[资源][docs_supported_resources]。在撰写本文时，有 89 个服务可以通过 CloudFormation 直接访问，稍后我们将看到如何使用不通过定制资源的服务。
    *   `Output` -模板的结果，通常作为`Parameters`输入到另一个模板中。
    *   需要注意的其他因素
    *   格式版本-标识模板的功能
    *   描述-不言自明
    *   元数据-提供关于模板的信息
    *   条件-通常用于定义是否创建资源，即如果环境类型为生产，则创建该资源
    *   transform——允许您执行模板片段(`AWS::Include`)或无服务器(又名 Lambda - `AWS:Serverless`)的宏。
*   stack Policies——IAM 风格的策略声明，控制什么可以改变以及谁可以改变它
*   可编程序性
    *   内部函数-助手函数。
    *   自定义资源
    *   `Transform`模板中的部分

### 内在函数(助手)

| 功能 | 用例 |
| --- | --- |
| Fn::Base64 | EC2 实例的用户数据 |
| Fn::Cidr | [CIDR][wiki_cidr]地址块/网络 |
| fn:findinmap | 按区域查找值，即 ami |
| Fn::GetAtt | 交叉引用模板(包括自身) |
| Fn:格塔兹 | 网络/子网 |
| Fn::ImportValue | 交叉引用模板(包括自身) |
| Fn::加入 | 将数组合并成字符串 |
| Fn::选择 | 从数组中选取一个值 |
| 联合国:分裂 | 将字符串转换为数组 |
| Fn::Sub | 用一个值代替另一个值 |
| Fn::转换 | 调用云形成宏 |
| 裁判员 | 交叉引用模板(包括自身) |
| Fn::然后呢 | 条件运算 |
| Fn::或者 | 条件运算 |
| Fn::等于 | 条件函数 |
| Fn:如果 | 条件函数 |
| Fn::不是 | 条件函数 |

### 资源属性

*   [creation policy][docs _ creation policy]要求资源发送信号，否则会发生超时

```
CreationPolicy:
  ResourceSignal:
    Count: '3' # 3 instances have been created, so 3 signals will need to be generated before fulfilling the CreationPolicy requirements
    Timeout: PT15M 
```

`Timeout`值是【ISO8601 持续时间格式】【wiki _ iso 8601 _ durations】:`PT#H#M#S`其中`#`是小时、分钟和秒的数量。给实例尽可能长的时间，如果超时太短，您将触发回滚。`PT15M`是 15 分钟的超时

要发送信号，需要在资源上安装名为[ `cfn-signal` ][docs_cfn_signals]的帮助脚本(通常在 EC2 实例的用户数据区完成)。

*   DeletionPolicy -定义当堆栈被删除时资源会发生什么。可能的值有:
    *   删除-默认
    *   保持
    *   快照-仅适用于 EBS、RDS 和 RedShift。存储快照的存储成本。
*   DependsOn -不保证该过程会成功完成
*   UpdatePolicy -定义当栈被更新时，资源会发生什么。
*   UpdateReplacePolicy -使用此选项保留/备份在堆栈更新期间被替换的资源的物理实例。

知道何时对 CreationPolicy 使用[Wait condition][docs _ Wait _ conditions]。

### 伪参数

是预定义的参数，返回当前上下文的值，即当前使用的帐户或区域。

| 参数 | 返回 |
| --- | --- |
| AWS::AccountId | 帐户 ID |
| AWS::NotificationARNs | 堆栈的通知 ARNs 列表 |
| AWS::NoValue | 使用`Fn::If`指定时，删除相应的资源 |
| AWS::分区 | 资源所在的分区只与中国和美国政府这样的专业区域相关 |
| AWS::地区 | 当前区域 |
| AWS::StackId | 当前创建的堆栈的 ID |
| AWS::StackName | 当前创建的堆栈的名称 |
| AWS::URLSuffix | 域名的后缀通常为 amazonaws.com，但可能因特定地区而异 |

### 嵌套堆叠

*   堆栈包含资源:S3 桶、EC2 实例和其他 AWS 服务
*   一个堆栈也可以包含另一个堆栈作为资源。
*   允许将复杂的基础设施拆分成易于管理的模板。
*   允许您避开堆栈限制(200/60/60 资源/输出/参数)

### 堆栈更新

堆栈策略仅适用于 JSON。

*   一般规则是允许一切，但拒绝特定的资源
*   没有堆栈策略意味着允许所有更新
*   一旦应用，就不能删除
*   一旦应用，默认情况下所有对象都受到保护，更新被拒绝

### 自定义资源

定制资源是一种提供和跟踪不直接通过 CloudFormation 支持的资源的方式。

请求/响应机制要么是 SNS 主题，要么是 Lambda 支持的[ARN][aws_arn]。

AWS 有一个[walk through][docs _ custom _ resource]演示了如何创建一个定制资源来执行 AMI 查找(使用 Lambda ),以便为给定的区域(在本例中是我们创建堆栈的区域)和 CPU 类型提供正确的 AMI。

在定制资源之前，您必须在模板的 Mappings 部分保存一个 ami 的静态列表。

## 为什么？

这允许您将基础设施定义为代码，而不是通过各种 ui(控制台和 CLI)执行的手动步骤

## 什么时候？

*   以系统和可重复的方式部署基础架构，而不是手动进行。
*   重复模式环境，即你主持 WordPress 业务，你有一个模板，为每个新客户部署网络服务器和数据库。
*   如果您正在使用自动化 CI/CD 环境，但希望扩展到蓝/绿(红/黑)环境，并创建环境的镜像以实现零宕机。
*   在 AWS 云的任何区域创建环境，无需手动重新配置，即 AMI 选择或子网分配。
*   您希望跟踪环境的变化，通过使用 CloudFormation 模板，这些模板可以存储在版本控制系统中，即 Git

## 如何？

这是一个非常基本的云形成的例子，我们将使用它来创建一个 S3 桶。

云形成模板:`hello-bucket.yaml`。

注意:为了保持简洁，我决定只使用 YAML 作为模板格式。CloudFormation 可以使用 JSON(事实上这是最初的格式，所以你仍然会发现很多这种格式的例子)。

```
Resources:
  HelloBucket:
    Type: AWS::S3::Bucket 
```

`HelloBucket`是我们的`resource`的逻辑名称，我们选择的类型是 S3 铲斗。

让我们使用 CLI 来创建基于该模板的堆栈。

```
aws cloudformation create-stack \
  --stack-name hellostack \
  --template-body \
  file:///path/to/hello-bucket.yaml 
{
    "StackId": "arn:aws:cloudformation:eu-west-3:xxx:stack/hellostack/4a3b0220-7552-11e9-acf0-0a230f532f04"
}  

aws cloudformation describe-stacks
{
    "Stacks": [
        {
            "StackId": "arn:aws:cloudformation:eu-west-3:xxx:stack/hellostack/4a3b0220-7552-11e9-acf0-0a230f532f04",
            "StackName": "hellostack",
            "CreationTime": "2019-05-13T07:39:50.524Z",
            "RollbackConfiguration": {},
            "StackStatus": "CREATE_COMPLETE",
            "DisableRollback": false,
            "NotificationARNs": [],
            "Tags": [],
            "DriftInformation": {
                "StackDriftStatus": "NOT_CHECKED"
            }
        }
    ]
} 
```

让我们检查一下堆栈创建的状态。

```
 aws cloudformation describe-stack-resources --stack-name hellostack
{
    "StackResources": [
        {
            "StackName": "hellostack",
            "StackId": "arn:aws:cloudformation:eu-west-3:xxx:stack/hellostack/4a3b0220-7552-11e9-acf0-0a230f532f04",
            "LogicalResourceId": "HelloBucket",
            "PhysicalResourceId": "hellostack-hellobucket-1ux8azkoq7t0t",
            "ResourceType": "AWS::S3::Bucket",
            "Timestamp": "2019-05-13T07:40:15.289Z",
            "ResourceStatus": "CREATE_COMPLETE",
            "DriftInformation": {
                "StackResourceDriftStatus": "NOT_CHECKED"
            }
        }
    ]
} 
```

有用的字段:

*   `StackName` -这是堆栈的逻辑名称(我们在 CLI 中使用`create-stack`子命令时定义的)
*   `PhysicalResourceId` -这是存储桶的名称，注意默认的命名约定:`<Stack Name>-<Logical Name of Resource in the Template>-<Random Hex String>`
*   `ResourceStatus` -这告诉我们资源创建是否成功。

我们可以使用`s3api`命令:
来验证 bucket 的名称

```
aws s3api list-buckets | jq '.Buckets[] | select(.Name | contains("hellostack"))'
{
  "Name": "hellostack-hellobucket-1ux8azkoq7t0t",
  "CreationDate": "2019-05-13T07:39:55.000Z"
} 
```

最后，让我们拆除，并验证桶已被删除。

```
aws cloudformation delete-stack --stack-name hellostack
# no output

aws s3api list-buckets | jq '.Buckets[] | select(.Name | contains("hellostack"))'
# no output 
```

## API 和 CLI 特性和动词

**特性**

*   堆栈[漂移/事件/策略/资源漂移]
    *   模板
    *   进口/出口
*   更改集合
*   堆栈集
    *   实例
    *   一组
    *   SetOperation
    *   SetOperationResults

* *动词(CRUD) **

*   大量

    *   创造
    *   列出/描述(堆栈)
    *   设置/更新/上传
    *   删除
*   更改集合

    *   创造
    *   列出/描述
    *   设置/更新/上传
    *   删除
*   堆栈集

    *   创造
    *   列出/描述
    *   设置/更新/上传
    *   删除

**离群值**

*   大量

    *   CancelUpdateStack
    *   ContinueUpdateRollback
    *   描述 StackDriftDetectionStatus
    *   估计模板成本
    *   GetTemplateSummary
    *   更新终止保护
    *   验证模板
*   更改集合

    *   执行变更集
*   堆栈集

    *   StopStackSetOperation

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*