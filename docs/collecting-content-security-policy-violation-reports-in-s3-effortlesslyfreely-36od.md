# 在 S3 收集内容安全策略违规报告(“毫不费力”/“自由”)

> 原文：<https://dev.to/hrbrmstr/collecting-content-security-policy-violation-reports-in-s3-effortlesslyfreely-36od>

在[之前的帖子](https://dev.to/hrbrmstr/wrangling-content-security-policies-in-r-2j1)中，我试图解释什么是内容安全策略(CSP)以及如何在 r 中使用它们。如果您没有 RTFPost，TLDR 是 CSP 让*您*控制可以与您的 web 内容一起加载的内容，并且可以选择配置为针对任何违反您创建的策略的尝试生成违规报告。虽然你不需要*指定一个报告 URI，但你真的应该这么做，因为至少你会知道你是否错误地错过了一个给定的主机、通配符或路径。你还会知道第三方和你的内容什么时候有恶意的或者仅仅是令人讨厌的活动(这是 CSP 的全部意义的一部分)。*

这篇文章有一个“R”类别标签(所以它击中了 R-bloggers 等人),因为它是关于在 R 中使用 CSP 的未编号系列的一部分，下一篇*文章将展示如何分析生成的 JSON 格式的报告。但是，要分析这样的报告，你*需要先找到它们*。因此，我们将在 Amazon AWS 中设置一个“无服务器”工作流，将 CSP 报告推送到 S3 一个组织良好的结构中，我们将能够从中访问、摄取和分析它们。*

当然，有一些服务会(免费合法地)让你把违规报告转发给他们，但是如果你能自己“免费”这样做，而不是把数据给第三方赚钱或表面上做善事，我无法理解放弃控制权的理由。

请注意，*所需要的*就是一个可通过互联网访问的 HTTPS 端点，它可以接受带有 JSON 有效负载的 HTTP POST 请求，然后将其存储在某个地方，所以如果您想，比如说，使用`plumber`包来处理这些请求而不求助于 AWS，那么请尽一切办法这样做！(还有，关于它的博客！)

### AWS“无服务器”CSP 报告工作流程先决条件

你显然需要一个亚马逊 AWS 账户，还需要安装 [AWS 命令行界面](https://aws.amazon.com/cli/)工具，以及一个有权使用 [CloudFormation](https://aws.amazon.com/cloudformation/) 的 IAM 用户。AWS 已经出现在*有一段时间了*,现在，注册 AWS，安装 CLI 工具和[生成 IAM 用户](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)充其量是多余的。[亚马逊有不错的介绍资源](https://aws.amazon.com/getting-started/)，老实说，现在是 2019 年，熟悉如何与至少一家云提供商合作是非常必要的技能，这取决于你在“技术”的哪个部分。如果您是 AWS 新手，那么请按照本段中的链接，浏览一些基础知识，然后跳回进入*四个*命令，您将需要运行这些命令来引导您的 CSP 收集设置。

### 在 AWS 中引导 S3 CSP 收集器

我们将使用[这个 CloudFormation 工作流](https://github.com/michaelbanfield/serverless-csp-report-to)来引导 CSP 收集过程，您应该浏览一下 [yaml 文件](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml)看看发生了什么。所说的 yaml 是“作为代码的基础设施”，这意味着它是一系列为您生成 AWS 服务的配置指令(即，没有指向和点击),也许更重要的是，如果您不想再保持这种活动状态，它会为您销毁这些服务。

[CF 输出指令](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml#L4-L14)将是您将在`report-uri` / `report-to` CSP 指令中使用的 URI，也是我们将在设置过程结束时查询的内容。

第一组[资源](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml#L4-L14)是 [AWS 胶水](https://aws.amazon.com/glue/)模板，这些模板能够将 CSP 报告结果连接到 [AWS Athena](https://aws.amazon.com/athena/) 中。Glue 是一个很好的 ETL 框架，但是如果设置为主动模式(Amazon 称之为“爬虫”模式)就有点贵，所以这个 CloudFormation 方法只创建了 Glue 模板，而没有激活它。这一部分可以被删除(正如回购作者所指出的)，但它不会造成任何伤害，也不会额外增加任何成本，所以保留它也是好的。

[下一位](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml#L64-L127)设置一个 [AWS 消防软管](https://aws.amazon.com/kinesis/data-firehose/)配置，这是一个听起来很傻的名字，用于设置存储“流”数据的工作流。这个“firehose”配置只是为 S3 存储桶设置一个路径，然后设置与所述存储桶相关联的必要权限。**这是我们在下一篇文章中要提取数据的地方。**

前面提到的“firehose”可以从各种输入源获取流数据，我们的数据源将是来自浏览器的 POSTed JSON HTTP 交互，因此我们需要一些东西来侦听这些 POST 请求，并将其连接到“firehose”。为此，我们需要一个 [API 网关](https://aws.amazon.com/api-gateway/)，这就是[倒数第二部分](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml#L128-L198)为我们设置的。它指示 AWS 设置一个 API 端点来监听 POST 请求，告诉它将要处理的数据类型(JSON ),然后告诉它要调用什么 [AWS Lambda](https://aws.amazon.com/lambda/) ,这在最后一节的[中。](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/template.yaml#L199-L213)

所述 lambda 代码在回购的 [index.js](https://github.com/michaelbanfield/serverless-csp-report-to/blob/master/index.js) 中，并且是一个简短的 Node.js 脚本，用于对 [CSP 报告 JSON](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only#Sample_violation_report) 进行后处理，使其在数据分析上下文中稍微更有用一些(鉴于字段名称中的`-`的自由使用，做出违规报告的人在创建结构时显然没有想到数据科学的人)。

如果以上听起来超级复杂，就去找 CSP 报告，你没有错。对于涉及 AWS 中多种类型移动部件的中等复杂工作流，我们权衡了自托管和保护独立但简单的 JSON 后处理服务器的成本和繁琐性。不利的一面是不得不更加熟悉 AWS 组件。有利的一面是，这几乎是免费的，除非你的网站非常受欢迎，要么经常受到 XSS 攻击，要么你的 CSP 政策配置不当。

你说‘免费’？!"是的。免费。(好吧，“大部分”免费)

*   **AWS API 网关**:每月 100 万次 HTTP REST API 调用(我们的 POST reqs 调用 lambda 代码)是免费的
*   **AWS Lambda** (向“消防水管”发送数据的`index.js`运行程序):每月 100 万个免费请求和每月 40 万秒的计算时间(运行`index.js`需要大约 1 秒)
*   AWS Firehose (将数据推送到 S3 的设备):第一个 500 TB/月是 0.029 美元
*   **AWS S3** :第一个 50 TB /月是 0.023 美元每 GB(CSP JSON 帖子 gzip'd 通常是< 1K 每个)+一些将数据放入 S3 和从 S3 复制数据的超分数(一分钱)成本。

一个精心制作的 CSP 和一个典型的网站应该最终花费你不到 1.00 美元/月，你可以通过[控制台](https://console.aws.amazon.com/billing/home?region=us-east-1#/freetier)或[的警报](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html)来监控这一切(如果需要，改变你的地区)。此外，您可以在任何时候用一个命令摧毁它(我们还没有建立它，所以我们将看到这一点)。

### 启动引导程序

正如回购所说，做:

```
$ git clone git@github.com:michaelbanfield/serverless-csp-report-to.git # get the repo
$ cd serverless-csp-report-to # go to the dir
$ aws s3 mb s3://some-unique-and-decent-bucket-name-to-hold-the-lambda-code/ # pick a good name that you'll recognize
$ aws cloudformation package \ # generate the build template
    --template-file template.yaml \
    --s3-bucket <bucket-you-just-created> \
    --output-template-file packaged-template.yaml

$ aws cloudformation deploy \ # launch the build
    --template-file /path/to/packaged-template.yaml \
    --stack-name CSPReporter \
    --capabilities CAPABILITY_IAM 
```

Enter fullscreen mode Exit fullscreen mode

这需要一两分钟的时间，完成后只需:

```
$ aws cloudformation describe-stacks \ 
    --query "Stacks[0].Outputs[0].OutputValue" \
    --output text \
    --stack-name CSPReporter 
```

Enter fullscreen mode Exit fullscreen mode

获取您将在报告指令中使用的 URL。

要清除所有这些创建的资源，你可以[进入控制台并执行](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html) *或*直接执行

```
$ aws cloudformation --delete-stack --stack-name CSPReporter 
```

Enter fullscreen mode Exit fullscreen mode

要查看为 CSP 报告创建的存储桶，只需:

```
$ aws s3 ls | grep firehose 
```

Enter fullscreen mode Exit fullscreen mode

### 鳍

如果你对 AWS 有经验，那可能没什么大不了。如果您是 AWS 新手或没有经验，这是获得“无服务器”API 设置经验的一个不错的方法，因为它便宜，易于删除，并且涉及 AWS 中的许多关键组件。

您可以浏览 AWS 控制台，查看所有创建的内容，并最终调整 CF yaml，使其符合您的意愿。

下一次我们将使用 r 深入分析 CSP 违规报告。

**提醒**to——不管出处(不管是我，RStudio，spiffy R 包作者，还是 AWS/微软/等大牌。)——*永远*至少抽查一下你将要安装或执行的代码。当涉及到在你的手机/平板电脑上安装来自应用商店的应用程序时，每个人都需要开始培养和磨练零信任心态，更不用说允许随机的 R、C[++]、Python、Go、rust、Haskel、…代码在你的笔记本电脑和服务器上执行了。这也是我浏览 YAML 的章节并特意链接到`index.js`的原因之一。不知道代码做什么可能会导致不幸的情况。

注意:如果你对此有一个替代的 [Terraform](https://www.terraform.io/) 配置，请在评论中注明，因为 TF 更“现代”，而不是以 AWS 为中心的“基础设施即代码”框架。此外，如果你已经用 Azure 或其他提供商完成了这项工作，也可以在评论中添加一条注释，因为它可能对那些对使用 AWS 不感兴趣的人有用。最后，如果你真的为此做了一个`plumber`服务器，也可以在帖子上写下你是如何做的，也许还可以讨论一下成本&的问题。