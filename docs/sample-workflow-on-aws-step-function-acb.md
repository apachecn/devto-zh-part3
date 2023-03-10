# AWS 步骤功能和示例工作流的简要指南

> 原文：<https://dev.to/mengjiann/sample-workflow-on-aws-step-function-acb>

# AWS 步进功能后台

AWS Step Functions 是 AWS 服务之一，它允许我们通过**集成 Lambda、Fargate、SNS 等现有 AWS 服务**来定义工作流。每个工作流都基于状态机的概念，并且使用**亚马逊国家语言** (ASL)(基于 JSON)来定义。

步骤功能的工作流程由一系列步骤组成，一个步骤的**输出作为下一个步骤**的输入。因此，您可以对工作流输入执行一系列操作。由于每个步骤之间传递数据的限制，最好使用像 AWS S3 这样的独立服务来传输大文件([限制](https://docs.aws.amazon.com/step-functions/latest/dg/limits.html))。

使用 Step 函数的好处是应用程序逻辑是在工作流中实现的，它与**单个组件** (step)的实现是分离的。这实际上促进了每个组件对于其他工作流的可重用性。

一个简单的状态机定义如下。`StartAt`和`States`是必选的积木。`StartAt`定义您工作流程的入口点，`States`定义您定制工作流程的步骤。在状态机中还有其他的[可选组件](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-state-machine-structure.html)。

```
{
  "Comment": "A Hello World example of the ASL.", // Optional
  "StartAt": "HelloWorld", // Compulsory
  "States": {              // Compulsory
    "HelloWorld": {
      "Type": "Pass",
      "Result": "Hello World!",
      "End": true
    }
  }
} 
```

为了定义工作流中的步骤，ACL 现在提供了以下可用的状态类型:

| 状态 | 目的 |
| --- | --- |
| 工作 | `Task`状态代表由状态机执行的单个工作单元。 |
| 及格 | 一个`Pass`状态将其输入传递到其输出，而不做功。 |
| 选择 | `Choice`状态向状态机添加分支逻辑。 |
| 等待 | 一个`Wait`状态延迟状态机继续运行一段指定的时间。您可以选择相对时间(从状态开始时开始，以秒为单位)或绝对结束时间(以时间戳为单位)。 |
| 成功 | `Succeed`状态成功停止执行。对于除了停止执行之外什么也不做的选择状态分支来说，成功状态是一个有用的目标。 |
| 失败 | 一个`Fail`状态停止状态机的执行，并将其标记为失败。 |
| 平行的 | `Parallel`状态可用于在您的状态机中创建并行执行分支。 |

# 示例步骤功能工作流程

本文将介绍 ASL 的一些有用特性，以及如何使用 AWS Lambda 函数创建一个简单的工作流。AWS Lambda 函数示例[可在此处](https://github.com/mengjiann/aws-step-functions-sample)获得。在开始之前，您需要从示例代码中部署 AWS Lambda 函数。如果你在 Lambda 部署上需要帮助，请点击这里查看[。](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-create-deployment-pkg.html)

示例步骤功能工作流程图如下:

[![Sample AWS Step Function Workflow](img/3a59622ad46620c383eea218c80ef3c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---cwxYAmF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vnhf9al5wnozi4s4woyj.png)

这个工作流程的基本思想是:
1)配置`random-number-generator-lambda`从一个数字范围生成一个随机数。
2)然后，触发`random-number-generator-lambda`产生一个随机数。
3)根据`random-number-generator-lambda`的输出，下一步是检查输出是高于还是低于数字 5。
4)如果高于 5，则触发 SNS 通知发送短信。
5)如果低于 5，则另一个λ`power-of-number-lambda`将被触发，以将先前的输入提升到配置指数的幂。然后，工作流程将在此之后结束。

下面是对这个示例工作流所使用的 ACL 特性的解释:
1)`random-number-generator-lambda-config`——通过使用`Pass`状态来配置`random-number-generator-lambda`。

```
"random-number-generator-lambda-config": {
  "Comment": "To configure the random-number-generator-lambda.",
  "Type": "Pass",
  "Result": {
      "min": 1,
      "max": 10
    },
  "ResultPath": "$",
  "Next": "random-number-generator-lambda"
}, 
```

2) `random-number-generator-lambda` -使用`Task`状态触发λ函数。

```
"random-number-generator-lambda": {
  "Comment": "Generate a number based on input.",
  "Type": "Task",
  "Resource": "${random-number-generator-lambda-aws-arn}",
  "Next": "send-notification-if-less-than-5"
}, 
```

3) `send-notification-if-less-than-5` -使用`Choice`状态检查上一步的输出是否小于 5。

```
"send-notification-if-less-than-5": {
  "Comment": "A choice state to decide to send out notification for <5 or trigger power of three lambda for >5.",
  "Type": "Choice",
  "Choices": [
    {
        "Variable": "$",
        "NumericGreaterThanEquals": 5,
        "Next": "power-of-three-lambda"
    },
    {
      "Variable": "$",
      "NumericLessThan": 5,
      "Next": "send-multiple-notification"
    }
  ]
}, 
```

4) `power-of-number-lambda` -如果输出大于 5，使用`Parameters`触发另一个 Lambda 函数来定制输入 JSON 对象。

```
"power-of-three-lambda": {
  "Comment": "Increase the input to power of 3 with customized input.",
  "Type": "Task",
  "Parameters" : {
    "base.$": "$",
    "exponent": 3
  },
  "Resource": "${power-of-number-lambda-aws-arn}",
  "End": true
}, 
```

5) `send-multiple-notification` -一个`Parallel`状态触发多个动作。
6)`send-sms-notification`——触发 SNS 通知发送短信。
7) `send-email-notification` -触发 SNS 通知发送邮件。(不作为样本的一部分)

```
"send-multiple-notification": {
  "Comment": "Trigger multiple notification using AWS SNS",
  "Type": "Parallel",
  "End": true,
  "Branches": [
    {
     "StartAt": "send-sms-notification",
     "States": {
        "send-sms-notification": {
          "Type": "Task",
          "Resource": "arn:aws:states:::sns:publish",
          "Parameters": {
            "Message": "SMS: Random number is less than 5 $",
            "PhoneNumber": "${valid-handphone-number}"
          },
          "End": true
        }
     }
   },
   {
    "StartAt": "send-sns-topic",
     "States": {
       "send-sns-topic": {
          "Type": "Task",
          "Resource": "arn:aws:states:::sns:publish",
          "Parameters": {
            "Message": "Email: Random number is less than 5: $",
            "TopicArn": "${aws-sns-topic-to-send-out-email}"
          },
          "End": true
        }
     }
   }  
  ]
} 
```

您可以使用步进功能控制台中的`Start Execution`按钮触发工作流程。每次调用都允许 JSON 对象作为工作流的输入，但这不是强制性的。或者可以配置 Cloudwatch 计划事件来触发工作流。

在此之前，确保您已经授予 IAM 角色执行 Step 函数的适当权限也很重要。对于此示例工作流，您将需要以下内容

*   AWS 托管策略- `AmazonSNSFullAccess` -触发 SNS 通知短信或电子邮件。
*   AWS Lambda 调用权限。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "lambda:InvokeFunction",
                "lambda:InvokeAsync"
            ],
            "Resource": "*"
        }
    ]
} 
```

如果执行成功，您应该会看到如下内容。一定要多试几轮，看看你能不能收到来自社交网站的短信通知！

[![Sample Invocation](img/1b8507995ceb56751acf5587e2b087e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CMQ4Q9RQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a6l5wcvy9bhgkaj1gicr.png)

上述示例工作流并未涵盖所有州，更多信息可从关于不同 ASL 功能的[官方文档](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)中找到。

希望这对你探索阶跃函数有用。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [蒙嘉恩](https://github.com/mengjiann)/[AWS-步骤-功能-样本](https://github.com/mengjiann/aws-step-functions-sample)

### AWS 步骤功能的示例工作流(使用 terraform)

<article class="markdown-body entry-content p-5" itemprop="text">

# AWS 步骤函数的示例项目

这是为了演示如何使用几个 AWS Lambda 函数在 AWS Step 函数上创建工作流。还提供了 Terraform 配置文件，以简化设置所需 AWS 资源的过程。

# AWS 函数

*   计算一个数的幂。
*   随机数生成器-lambda -生成一个介于最小值和最大值之间的随机数。

# 地形-先决条件

*   要使用 terraform，请点击[此处](https://learn.hashicorp.com/terraform/getting-started/install.html)安装并学习哈希公司的 Terraform
    *   对于自制软件用户:`brew install terraform`
*   拥有 AdministratorAccess 的 AWS 用户(编程访问)。复制`access key ID`和`secret access key`以便稍后设置`awscli`。
*   从官方[指南](https://docs.aws.amazon.com/cli/latest/userguide/install-linux-al2017.html)安装`awscli`
    *   对于自制软件用户:`brew install awscli`
*   使用`access key ID`和`secret access key`，按照这个[指南](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)为你的`awscli`踏出新的一步。

# 地形指南

*   在`git clone`之后，将目录更改为…

</article>

[View on GitHub](https://github.com/mengjiann/aws-step-functions-sample)

参考:

*   [AWS 步进功能](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)
*   [AWS 国家语言官方文档](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)
*   [AWS 状态语言-不同状态](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-states.html)