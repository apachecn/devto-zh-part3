# AWS 概念从 A 到 Z

> 原文：<https://dev.to/helenanders26/aws-series-aws-from-a-to-z-45b1>

掌握 AWS 服务可能是一个相当大的挑战。我的术语表受到了简明英语中的 AWS 的启发，这确保了我能够区分我的 Athena 和我的 Aurora 以及我的 CloudFront。

[![AWS](img/087f07fdd303d18602d81ca65bab2347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3XLa1PIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79zge7qikw4haemrlnz8.png)

* * *

[报警](#chapter-1)
[斗](#chapter-2)
[CLI](#chapter-3)
[DB 快照](#chapter-4)
[边缘位置](#chapter-5)
[消防软管](#chapter-6)
[组](#chapter-7)
[托管区](#chapter-8)
[实例类型](#chapter-9)
[作业流程](#chapter-10)
[KMS](#chapter-11)
[生命周期](#chapter-12)
[缩放](#chapter-19)
[标记](#chapter-20)
[单位](#chapter-21)
[虚拟私有云](#chapter-22)
[WAF](#chapter-23)
[x . 509 证书](#chapter-24)
[Yobibyte](#chapter-25)
[专区](#chapter-26)

* * *

### 报警

当您第一次开始使用 Amazon Web Services 时，您可能想要添加一个计费提醒。确保你不会遇到意想不到的费用是很重要的，因为很容易忘记一些东西正在运行。

[![kylegalbraith](img/88a6312db831343b6c7c3b1b5bea970e.png)](/kylegalbraith) [## 如何在忘记之前更好地查看 AWS 成本

### 凯尔·加尔布雷斯 10 月 8 日 186 分钟阅读

#aws #devops #devtips #startup](/kylegalbraith/how-to-better-watch-your-aws-costs-before-you-forget-566g)

* * *

### 斗

一个 [S3 桶](https://aws.amazon.com/s3/)是存储对象的地方，类似于你本地机器上的文件和文件夹。每个对象包括:

*   **键** -对象的名称
*   **值** -文件中的数据本身由字节组成
*   **VersionID**
*   **元数据**

有四个存储层:

*   **S3** -最昂贵、最可靠的选择
*   **S3:IA** -用于存储不容易复制且需要快速检索的非关键数据
*   **S3:IA-One Zone** -用于存储易于复制且需要快速检索的非关键数据。
*   **冰川**——极便宜的长期储存
*   **深冰川** -用于长期存储，12 小时检索“冷”数据

和大卫一起了解更多关于 S3 的信息

[![david_ojeda](img/3982752e278540bbb2ea6a3d4a365f5b.png)](/david_ojeda) [## 奥斯 S3 角。1 -基础知识

### 大卫奥赫达 17 年 8 月 18 日 3 分钟阅读

#aws #s3](/david_ojeda/aws-s3-pt-1---the-basics-55bp)

* * *

### CLI

[AWS CLI](https://aws.amazon.com/cli/) 允许您从命令行发出命令。这对于上传文件到 S3 桶和启动 EC2 实例很有用。

了解有关 CLI 的更多信息:

[![mdminhazulhaque](img/56bdc441a64f1b150717b40eae45653a.png)](/mdminhazulhaque) [## AWS CLI 备忘单

### 医学博士 Minhazul Haque 4 月 15 日 20 时 16 分阅读

#aws #jq #devops](/mdminhazulhaque/aws-cli-cheatsheet-15f2)

* * *

### 数据库快照

[Amazon RDS](https://aws.amazon.com/rds/) 为您的整个实例创建一个存储卷快照。创建此快照会导致短暂的 I/O 暂停，持续时间从几秒到几分钟不等。多 AZ 数据库实例不受 I/O 暂停的影响，因为备份是在备用数据库上进行的。

创建数据库快照时，需要确定要备份哪个数据库实例，然后为数据库快照命名，以便以后可以从中恢复。您可以使用 AWS 管理控制台、AWS CLI 或 RDS API 来完成此操作。

与 Jeremy 一起学习如何自动化这一过程

[![goozenburger](img/31587b735fc9529241b6dffa8089bd57.png)](/goozenburger) [## 使用 AWS RDS 自动将生产数据库恢复到测试中

### 杰里米冈兹伯格 1818 年 5 月 8 日 14 分钟阅读

#aws #rds #sqlserver #aurora](/goozenburger/automating-restoration-of-production-databases-into-test-using-aws-rds-56f4)

* * *

### 边缘位置

[亚马逊 CloudFront](https://aws.amazon.com/cloudfront/) 就是 AWS CDN。它缓存离用户最近的信息，以便下一个用户可以更快地下载副本。CloudFront 可以分发所有网站内容，包括来自 S3 等 AWS 服务或您自己的非 AWS 服务器的动态、静态、流式和交互式内容。

与 Kyle 一起了解更多关于 CloudFront 的信息

[![kylegalbraith](img/88a6312db831343b6c7c3b1b5bea970e.png)](/kylegalbraith) [## 如何在 AWS 中轻松提升静态网站的交付

### 凯尔·加尔布雷斯 1918 年 10 月 1 日 8 分钟阅读

#webdev #aws #devops #tips](/kylegalbraith/how-to-easily-boost-the-delivery-of-static-websites-inaws-m46)

* * *

### 消防水带

亚马逊 Kinesis Data Firehose 是一种近乎实时传输数据的可靠方式。数据可以传输到 S3、亚马逊的数据仓库解决方案、[红移](https://dev.to/ronsoak/the-r-a-g-redshift-analyst-guide-what-is-redshift-fc1)或[弹性搜索](https://aws.amazon.com/elasticsearch-service/)。

Kinesis 允许数据从生产者实时流向加工者或存储选项。与批处理相比，这是一个巨大的变化，批处理是将数据从一个位置传送到另一个位置的传统方式。

[![AWS Community Builders ](img/61a89e9c81848ea2692d074797adc865.png)![](img/cf8f4743f7491ddc46e8ef58f47ecce7.png)](/aws-builders) [## 事件流和 AWS Kinesis

### 面向 AWS 社区构建者的 Supratip Banerjee 月 26 日 214 分钟阅读

#aws #bigdata #eventdriven #architecture](/aws-builders/event-streaming-and-aws-kinesis-4877)

* * *

### 组

[AWS 识别和访问管理](https://aws.amazon.com/iam/)允许您安全地控制个人和团体对您的资源的访问。

默认情况下，在您为用户分配角色之前，用户没有访问权限。角色定义了发出 AWS 服务请求的一组权限，通常用于为用户组分配执行任务或访问服务的权限。

通过 [David 的](https://dev.to/david_ojeda)实际例子了解更多关于 IAM 的信息:

[![david_ojeda](img/3982752e278540bbb2ea6a3d4a365f5b.png)](/david_ojeda) [## AWS IAM 角。2 -一个实际例子

### 大卫·奥赫达 7 月 31 日 186 分钟阅读

#aws #iam](/david_ojeda/aws-iam-pt-2---a-practical-example-13b6)

* * *

### 托管区

[亚马逊 Route 53](https://aws.amazon.com/route53/) 是亚马逊的域名系统(DNS)网络服务。它旨在为开发人员提供一种经济高效的方式，通过将 www.example.com[等名字翻译成计算机用来相互连接的 192.0.2.1 等数字 IP 地址，将最终用户路由到互联网应用程序。](http://www.example.com)

AWS 将服务路由命名为 53，因为所有 DNS 请求都是通过端口 53 处理的。

了解更多关于使用 [Mario](https://dev.to/mariordev) 托管静态网站的信息

[![mariordev](img/ced718d586c73d38b13ee33f226c370b.png)](/mariordev) [## 在 AWS S3 上托管一个静态站点

### 马里奥 1 月 11 日 184 分钟阅读

#static #aws #s3 #dns](/mariordev/host-a-static-site-on-aws-s3-5ai9)

* * *

### 实例类型

EC2 是一种在云中提供虚拟机的服务，您只需为使用的容量付费，并从适合不同使用情形的“系列”实例类型中进行选择。

**通用** -计算、内存和网络资源的平衡
**计算优化** -适用于受益于高性能处理器
**内存优化** -在内存中处理大型数据集的工作负载的快速性能
**加速优化** -硬件加速器，或协处理器
**存储优化** -对本地存储上非常大的数据集进行高度、顺序的读写访问

[![helenanders26](img/a0d1b41c96c04d34ab697b9304156601.png)](/helenanders26) [## 哪款 EC2 适合你？

### 海伦·安德森 1916 年 4 月 14 日阅读

#beginners #aws #architect](/helenanders26/aws-series-which-ec2-is-right-for-you-3f83)

* * *

### 作业流程

[Amazon EMR](https://aws.amazon.com/emr/) 提供了一个可扩展的框架，因此您可以在 S3 数据湖上运行 Spark 和 Hadoop 流程。EMR 模板上的运行作业根据提供的参数启动 Amazon EMR 集群，并根据指定的时间表开始运行步骤。一旦作业完成，EMR 集群就会终止。

AWS 为数据科学和机器学习提供的其他服务越来越多，请通过 [Julien](https://dev.to/juliensimon) 了解更多信息

[![juliensimon](img/c695e292e53900c90e7fcbfee28ae586.png)](/juliensimon) [## 亚马逊 SageMaker:总结 6 个月的客户会议

### 朱利安·西蒙 7 月 31 日 187 分钟阅读

#ai #datascience #machinelearning](/juliensimon/amazon-sagemaker-summing-up-6-months-of-customer-meetings-3o27)

* * *

### KMS

AWS KMS 服务使得在 AWS 上创建和控制加密密钥变得容易，然后可以利用这些密钥以安全的方式加密和解密数据。该服务利用硬件安全模块(HSM ),反过来保证生成的密钥的安全性和完整性。

了解更多有关如何开始使用 [Lou](https://dev.to/loujaybee) 的信息

[![loujaybee](img/8024036da3f0e59e1561fac5315bd545.png)](/loujaybee) [## 如何在 AWS Lambda 中使用 AWS KMS

### 娄(🚀打开云☁️)3 月 9 日 208 分钟阅读

#aws #serverless #devops #cloud](/loujaybee/how-to-use-aws-kms-in-aws-lambda-10ik)

* * *

### 生命周期

如果您希望经济高效地存储对象，请配置它们的生命周期。

生命周期配置定义了亚马逊 S3 应用于一组对象的操作。例如，您可能在创建对象一年后将其存档在 Glacier 中，或者在创建 30 天后将其转移到 S3:IA。

通过这个实例了解更多信息:

[![veermanhas](img/7b408a8c26b244de25542b78c97a076e.png)](/veermanhas) [## 如何轻松删除大型 S3 桶？

### veer Abheek Singh 4 月 11 日 183 分钟阅读

#aws #explainlikeimfive](/veermanhas/how-to-easily-delete-large-s3-buckets-f2e)

* * *

### 消息

[Amazon SNS](https://aws.amazon.com/sns/) 允许应用程序通过“推送”机制向多个用户发送时间关键的消息，无需定期检查或“轮询”更新

亚马逊 SQS 将消息存储在队列中。SQS 不能传递任何消息，需要外部服务(lambda、EC2 等)来轮询 SQS 并从 SQS 获取消息。

通过同时使用亚马逊社交网络和亚马逊 SQS，消息可以被发送到需要即时事件通知的应用程序，也可以保存在亚马逊 SQS 队列中，供其他应用程序稍后处理。

与 [Frank](https://dev.to/frosnerd) 一起了解更多这一切是如何结合在一起的

[![frosnerd](img/002ddb6873ad040b9a1603b02a4f8c5a.png)](/frosnerd) [## AWS 中使用 SNS、SQS 和 Lambda 的事件处理

### 弗兰克·罗斯纳 6 月 29 日 1811 分钟阅读

#aws #cloud #lambda #showdev](/frosnerd/event-handling-in-aws-using-sns-sqs-and-lambda-2ng)

* * *

### NAT 网关

一个[虚拟私有云](https://aws.amazon.com/vpc/) (VPC)是一个专用于你的 AWS 账户的虚拟网络。它在逻辑上与 AWS 云中的其他虚拟网络隔离。您可以将 AWS 资源(如 Amazon EC2 实例)启动到您的 VPC 中。

您可以使用 NAT 设备使专用子网中的实例能够连接到互联网(例如，用于软件更新)或其他 AWS 服务，但阻止互联网启动与实例的连接。NAT 设备将流量从私有子网中的实例转发到互联网或其他 AWS 服务，然后将响应发送回实例。

[![grahamlyons](img/5bee69a7849ea7cf7fc44f33acaa6e41.png)](/grahamlyons) [## 关于 AWS 网络，您需要了解的一切

### 格雷厄姆里昂 1 月 28 日 1815 分钟阅读

#aws #networking](/grahamlyons/everything-you-need-to-know-about-networking-on-aws-4bkf)

* * *

### 按需实例

有多种方式为亚马逊 EC2 实例付款:

*   **按需** -根据您运行的实例，按每小时或每秒付费。
*   **保留的实例** -以比按需价格低 75%的价格提供保留，让您对在需要时启动实例的能力充满信心。
*   **Spot Instances** -以高达按需价格 90%的折扣请求备用亚马逊 EC2 计算能力。
*   **专用主机**——在专用的物理服务器上提供 EC2 实例容量。
*   **节省计划**——提供保留实例的好处，但在利用节省的同时，在同一系列内更灵活地更改实例类型

与 [Chris](https://dev.to/liquid_chickens) 一起了解更多定价信息

[![liquid_chickens](img/09ad961e2957c3601d8cb52592eef189.png)](/liquid_chickens) [## 如何降低您的 AWS EC2 账单

### 克里斯·多兹 4 月 8 日 185 分钟阅读

#devops #aws #business](/liquid_chickens/how-to-lower-your-aws-ec2-bill-5l4)

* * *

### 持久存储

[Amazon EBS](https://aws.amazon.com/ebs/) 是一个持久存储设备，可以连接到一个**单个** EC2 实例，用作数据库、应用托管和存储的文件系统。

[亚马逊 EFS](https://aws.amazon.com/efs/) 是一个托管网络文件系统，可以在**多个**亚马逊 EC2 实例之间共享，并且可以根据工作负载进行扩展。

了解有关使用 [Ashan](https://dev.to/ashanfernando) 供应 EBS 的更多信息

[![ashanfernando](img/b2bef76e2d4b4cfb3931af580560a477.png)](/ashanfernando) [## AWS 中 EBS 卷容量规划的重要性

### ashan Fernando 2 月 1 日 184 分钟阅读

#aws #storage #ebsvolumes #cloud](/ashanfernando/the-importance-of-capacity-planning-for-ebs-volumes-in-aws-3pd0)

* * *

### 查询

[Amazon RDS](https://aws.amazon.com/rds/) 使得在云中提供托管数据库实例变得很容易。在撰写本文时，以下数据库引擎是可用的。

*   用于 MySQL 和 PostgreSQL 的亚马逊 Aurora
*   关系型数据库
*   一种数据库系统
*   MariaDB
*   神谕
*   MS SQL Server

如果 NoSQL 数据库更合适，AWS 提供了 [DynamoDB](https://aws.amazon.com/dynamodb/) 。[网飞](https://www.youtube.com/watch?v=k8PTetgYzLA)使用 DynamoDB 运行它的 A/B 测试和个性化实验。

通过 [Ivan](https://dev.to/mushketyk) 了解更多关于 DynamoDB 的信息

[![mushketyk](img/a1144d8937897386d76128c5cbaa7b4f.png)](/mushketyk) [## 应该使用 DynamoDB 吗？

### 伊万·穆什克特克 11 月 6 日 17 时 13 分阅读

#aws #database #dynamodb](/mushketyk/should-you-use-dynamodb-5m5)

* * *

### 读取副本

[读取复制](https://aws.amazon.com/rds/details/read-replicas/)可以成为灾难恢复计划的一部分。复制意味着辅助数据库处于在线状态，可以被查询。这不仅有利于灾难恢复，而且如果您将一个实例用于报告，一个实例用于实时查询，这也很有用。

如果你使用的是 AWS，只需要点击几下就可以设置好了。如果源数据库实例出现故障，您可以提升读取副本，或者将流量路由到此处以减少源数据库上的负载。

[![klauenboesch](img/c0e2606beb7f6d778b5d61432c0f430f.png)](/klauenboesch) [## 使用读取副本在 AWS 中备份 MySQL

### 基督教 2011 年 4 月 20 日 5 分钟阅读

#aws #blog #devops #automation](/klauenboesch/backing-up-mysql-in-aws-using-a-read-replica-3ia6)

* * *

### 缩放

Auto Scaling 根据用户定义的策略、计划和警报自动启动和终止 Amazon EC2 实例。您可以使用 Auto Scaling 来维护 AWS EC2 实例，这些实例可以根据任何负载进行调整。还可以使用自动缩放一次在一个组中显示多个实例。

从 [Ashan](https://dev.to/ashanfernando) 的这个概述中了解更多信息

[![ashanfernando](img/b2bef76e2d4b4cfb3931af580560a477.png)](/ashanfernando) [## AWS 中自动伸缩和负载平衡 EC2 实例的部署策略

### 亚山费尔南多 2 月 26 日 183 分钟阅读

#aws #autoscaling #loadbalancing #cloud](/ashanfernando/deployment-strategies-for-auto-scaling-and-load-balancing-ec2-instances-in-aws--2e6l)

* * *

### 标注

在您的[元数据](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)中使用标签有助于识别谁在使用每种资源，并获得对成本的一些控制。然后，您可以将这些与[月度账单报告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/configurecostallocreport.html)结合使用

与 [Michael](https://dev.to/sensedeep) 一起了解更多关于您在尝试管理成本时应该考虑的事项

[![sensedeep](img/230f294c3f8f1c0209ba0e6b99d7b3fe.png)](/sensedeep) [## AWS 云成本清单 V2

### 迈克尔·奥布莱恩 10 月 3 日 185 分钟阅读

#cloud #aws #webdev #devops](/sensedeep/aws-cloud-cost-checklist-v2-2o92)

* * *

### 单位

CloudWatch 是基于度量的。这些指标代表一组按时间排序并发布到 CloudWatch 的数据点。将指标想象成一个要随时间监控的变量，数据点代表它的值。

每个数据点都有一个时间戳和测量单位。当您请求统计信息时，返回的数据流包含名称空间、度量名称、维度和单位信息。

通过使用 Lambda、Cloudwatch 和 SNS 创建 CloudWatch 警报，了解如何与 Alex 一起将这一点付诸实践。

[![alex_barashkov](img/2930b6ca7bf008d7498f986337a7485d.png)](/alex_barashkov) [## 如何向 Slack 发送 AWS CloudWatch 警报

### 亚历克斯·巴拉什科夫 10 月 18 日 184 分钟阅读

#beginners #devops #opensource #howto](/alex_barashkov/how-to-send-aws-cloudwatch-alarms-to-slack-596e)

* * *

### VPC

[虚拟私有云(VPC)](https://aws.amazon.com/vpc/) 是一个虚拟数据中心，位于逻辑隔离的 AWS 可用性区域内。VPC 的组件包括互联网网关/虚拟专用网关、路由表、网络访问控制列表、子网和安全组。

[![helenanders26](img/a0d1b41c96c04d34ab697b9304156601.png)](/helenanders26) [## 为什么 VPC 像伦敦地铁

### 海伦·安德森 8 月 13 日 195 分钟阅读

#aws #vpc #networking](/helenanders26/aws-series-why-a-vpc-is-like-the-london-underground-k7p)

* * *

### 晶圆

[AWS Web 应用程序防火墙(WAF)](https://aws.amazon.com/waf/faqs/) 通过根据您创建的规则过滤流量，保护 Web 应用程序免受特定用户代理、不良机器人或内容抓取器等攻击。

**阻止超过请求限制的 IP 地址**
这让您可以通过 IP 地址、国家、阻止 SQL 注入、恶意脚本和请求长度来控制访问。

**阻止提交错误请求的 IP 地址**
该解决方案允许您使用 Lambda、CloudWatch 和 AWS WAF 来阻止 IP 地址，以便在达到阈值后阻止请求。

AWS WAF 可以部署在 Amazon CloudFront 上，在边缘位置保护您的资源和内容。和应用程序负载平衡器(ALB)来保护面向互联网的负载平衡器以及内部负载平衡器。

了解更多关于使用 Cloudfront [和 Rob](https://dev.to/rob117) 让它为您工作的信息

[![rob117](img/3a92e220b5f40087f749277f1fec5d09.png)](/rob117) [## AWS 云的无服务器后端:Cloudfront 和 WAF

### 罗布·斯特林

#cloudfront #s3 #staticsites #waf](/rob117/serverless-backends-with-aws-cloud-cloudfront-and-waf)

* * *

### X.509 证书

您可以在 [AWS 证书管理器](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)中使用 x.509 证书在内部识别用户、计算机、应用程序、服务、服务器和其他设备。

了解这是如何与埃里克一起工作的

[![baens](img/519afe6bd785a0307785e9e7db271567.png)](/baens) [## 为 Postgres 和 vault 设置开发工作区

### 埃里克·林德姆 27 岁 18 岁 8 分钟阅读

#docker #vault #postgres](/baens/setting-up-a-development-workspace-for-postgres-and-vault-439p)

* * *

### 生物字节

好吧，我在这里作弊了，但这是一个真正有趣的帖子，来自[凯文](https://dev.to/kbk0125)并把所有这些放在一起。

有趣的事实:一兆字节是 2^80 或 1，208，925，819，614，629，174，706，176 字节。

[![kbk0125](img/adf31106495387012f2bda8711ca7d7b.png)](/kbk0125) [## 亚马逊网络服务(AWS)通过运营一家啤酒厂来解释

### 凯文·科诺年科 8 月 30 日 188 分钟阅读

#webdev #beginners #tutorial](/kbk0125/amazon-web-services-aws-explained-by-operating-abrewery-2j0)

* * *

### 地带

需要理解的最重要的介绍性概念之一是，AWS 将其基础设施托管在名为[可用性区域(AZs)](https://aws.amazon.com/about-aws/global-infrastructure/) 的数据中心。一个地区有多个 AZ，这意味着如果一个 AZ 出现问题，另一个 AZ 可以接手。对于某些服务，您可以在多个区域托管您的应用程序。

了解为什么在与 [Frank](https://dev.to/frosnerd) 一起构建 AWS 时应该考虑这一点

[![frosnerd](img/002ddb6873ad040b9a1603b02a4f8c5a.png)](/frosnerd) [## 部署 AWS RDS MySQL 实例并进行基准测试

### 弗兰克·罗斯纳 6 月 22 日 1815 分钟阅读

#aws #cloud #networking #mysql](/frosnerd/deploying-and-benchmarking-an-aws-rds-mysql-instance-2faf)

* * *

这篇文章最初出现在[helenanderson.co.nz](http://www.helenanderson.co.nz/aws-a-z/)