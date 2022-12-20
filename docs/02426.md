# 使用 Terraform 和 Salt 在 AWS 上部署 HA WordPress

> 原文：<https://dev.to/tomwerneruk/deploying-ha-wordpress-on-aws-using-terraform-and-salt-4144>

[![Deploying HA WordPress on AWS using Terraform and Salt](img/524ff1fc8323866656d976a72e59560f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3c3zlyH1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1515646557491-e2615816e7f8%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

不管你喜不喜欢，WordPress 仍然存在。由于有大量的插件、主题和体验，它仍然是创建网站的首选。

在尝试学习任何东西时，我发现脚本化部署是从不同角度学习产品的一个很好的方式，需要对所有移动部分进行更深入的思考。这是围绕自动化 AWS 部署的几篇文章中的第一篇。我有造云的经验，但是为了拓展我的技能，我开始使用地形。我将使用 Salt 来自动将 WordPress 部署到 AWS 基础设施上。

这篇文章将帮助您启动并运行 WordPress 的高可用性(h a)部署，为下一个大事件做好准备。它假设你对 AWS 和 WordPress 有所了解。

## 设计就是要权衡取舍

设计 IT 架构绝对不是非黑即白的，它是一个由假设、估计、以前的经验和限制(知识、财务和环境等等)组成的复杂的混合体。除了最简单的设计概要，给两个建筑师同样的概要，你会得到两个不同的设计。所有的设计本质上都是固执己见的。

这和我们的 WordPress HA 解决方案有什么关系？表面上听起来很简单，但真的是这样吗？...

例如，你说的“哈”是什么意思？维护期间不睡觉？对 AWS 故障具有弹性？对 WordPress 问题有弹性(坏插件，畸形更新)？

你的制约因素是什么？你每月的花费预算是多少？你有什么专业知识来保持这种解决方案？实际上，你能容忍多长时间的停机？(可能比你想象的要多)。

出于以上，有多少是确定的？估计值是多少(知情或猜测)？你认为你需要多少钱？

在着手这个项目时，我试图分解每个功能组件，并评估最适合这个任务的 AWS 产品。我试图根据 AWS 架构良好的框架支柱来评估每个选项，而不仅仅是“听起来正确”的选项。

| 系统元件 | 低 Fi 解决方案 | 常用解决方案 | 高级/特殊要求解决方案 | 我会选择什么？ |
| --- | --- | --- | --- | --- |
| 边缘 | DNS 循环调度(Route53) +运行状况监视器 | AWS ELB(应用程序负载平衡器) | AWS ELB(经典或 ALB) | 对于大多数网站，AWS ELB 在应用模式。它具有成本效益，扩展性好，需要最少的集成工作，并且在概念上易于理解。在不惜一切代价要求吞吐量的情况下，可能需要 Classic。当需要平衡非 TCP 流量时，需要使用循环调度。 |
| 计算 | EC2 实例 | 自动缩放组中的 EC2 实例 | 自动缩放组中的 EC2 实例 | 使用自动缩放组在这里是显而易见的。额外的学习曲线值得操作上的便利。除非配置不当，否则不会增加成本。 |
| 数据库ˌ资料库 | EC2 托管的 MySQL 实例 | RDS 多 AZ 部署 | RDS 多 AZ +跨区域读取副本 | 我会在这里为多 AZ 部署而努力。如果您需要容忍 AWS 区域故障，那么您将需要考虑跨区域读取副本(使用自定义故障转移机制来提升读取副本并修改 WordPress 配置)。 |
| WordPress 根存储 | 不适用，烘焙成 AMI | 来自 S3 的推/拉 | electrical field stimulation 电场刺激 | 一路 EFS。成本更高，但操作更灵活，更不容易出错。如果短时间内在多个 WordPress 主机上发生文件系统更新，使用 cronjob 或类似工具同步到 S3 可能会有奇怪的并发问题。考虑到运营优势，尤其是在使用 IA 存储级别时，经济高效。 |
| 对象存储 | 没有对象存储，只提供来自 EC2 实例的静态资源 | S3 +云锋 | S3 +云锋 | S3 一直到这里，允许媒体通过 Cloudfront 交付网络浮出水面。 |

## Terraform 快速入门

Terraform 非常容易上手。它部署和使用都很简单，语法也很清晰。

我不打算在这里一步一步地写如何安装和运行 Terraform，而是去[我的频道看 Windows 和 Linux 的教程](https://www.youtube.com/channel/UCkn5kcB10r7wx1MkRuSixXQ)。相反，我想介绍 Terraform 工作流的原理以及如何使用模块。我也强烈推荐[来自 HashiCorp (Terraform 的创造者)的教程曲目](https://learn.hashicorp.com/terraform/)。

Terraform 有一个简单的架构。它包括`terraform`工具，当在一个目录中运行时，该工具可以:试运行(`plan`)、部署(`apply`)或移除(`destroy`)在一个或多个模块中定义的基础设施。这是管理此部署所需的 3 个核心命令。除了与您的提供商的连接性(在这种情况下是对 AWS 的互联网访问)之外，对于工具的安装或运行位置没有特殊要求。

除了像 Terraform 一样使用基础设施作为代码工具的自动化好处之外，我们应该远离重新发明轮子，更像一个开发人员那样思考，尽可能使用库。在 Terraform 中，基础设施定义可以被包装到一个模块中，以允许它在其他地方重用，就像一个编程库一样。Terraform 可以访问位于 [Terraform 注册表](https://registry.terraform.io/)中的一个大型现成模块库。本教程将广泛使用注册表中的 AWS 模块。我毫不怀疑这些模块的作者可能比我更了解 AWS 和 Terraform！本教程将使用一个简化的文件夹结构，我会适应，以促进现实世界的使用。有各种方法来实现一个为现实世界使用做好准备的结构，查看最后的链接推荐阅读。

为此，我们的结构将是这样的:

```
├── main.tf
├── outputs.tf
├── modules
│   ├── compute
│   │   ├── main.tf
│   │   ├── userdata.tmpl
│   │   └── variables.tf
│   ├── database
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── efs
│   │   ├── main.tf
│   │   ├── output.tf
│   │   └── variables.tf
│   ├── media
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── network
│   │   ├── main.tf
│   │   └── outputs.tf
│   └── seeder
│   ├── main.tf
│   └── variables.tf
├── packer
│   ├── aws_vars.json
│   └── template.json
├── salt_tree
│   └── srv
│   ├── pillar
│   └── salt
├── terraform.tfvars
└── wordpressha.pem 
```

此源树包含；

这包含了所有模块将被调用的入口点。

`modules`包含我们根据上述设计分析得出的功能部分。

`packer`包含 AMI 基本映像的模板。参见[使用包装材料和盐制作灯 AMI](https://dev.to/tomwerneruk/creating-a-lamp-ami-using-packer-and-salt-507j-temp-slug-6995235)了解如何使用。

Packer 和 Terraform 都使用`salt_tree`在我们部署的 EC2 实例上配置我们的 WordPress 安装。你可以很容易地把它换成不同的工具，比如 Chef 或 Puppet，并相应地改变 Terraform 代码中的 provisioner。

`terraform.tfvars`包含支持我们解决方案的配置值。运行 Terraform 之前，需要填写空字段。

## 部署到 AWS🎉

理论到此为止，如果你已经走了这么远，你就在最后冲刺阶段了！

假设您已经正确安装了 Terraform 和 Packer，请从我的 Git 存储库中检查代码，该存储库位于 https://Git lab . com/fluffy-clouds-and-lines/ha-WordPress-using-terra form-and-salt . Git。

在继续运行 Terraform 之前，我们需要一个 SSH 密钥对(Terraform 目前无法创建它们)。创建您的密钥对；

*   登录到 AWS 控制台，
*   切换到您的目标区域，并打开 EC2，
*   网络与安全>密钥对>创建密钥对
*   将密钥对命名为“wordpressha ”,并将下载的 wordpressha.pem 复制到 Terraform 代码已签入的目录中，
*   在 Linux 上，将权限更改为 400(用户只读)。

接下来，确保完成`terraform.tfvars`。一旦完成，执行；

```
> packer build -var-file=./packer/aws_vars.json ./packer/template.json
> terraform init
# Terraform modules for RDS and VPC don't resolve dependancies correctly, so explictly build VPC first
> terraform apply -target=module.network 
# Deploy Seeder dependancies
> terraform apply -target=module.database -target=module.efs 
# Deploy seeder
> terraform apply -target=module.seeder
# Deploy all to make state consistent
> terraform apply 
```

从头到尾大约需要 15 分钟。这份遗嘱；

*   构建我们的定制 AMI 映像，内置所有 LAMP (Apache、MySQL、PHP)依赖项，
*   下载外部地形模块，
*   建造 VPC 自动气象站，
*   部署 S3 bucket 和 CloudFront 发行版，
*   创建应用程序负载平衡器和自动扩展组，
*   部署 RDS MySQL 数据库实例，
*   创建弹性文件系统，
*   部署“WordPress 种子程序”。这将挂载 EFS 并安装 WordPress，这样作为自动缩放组的一部分启动的节点已经可以使用 WordPress 安装，
*   将 A 记录发布到 Route53，链接到 ALB。

然后你应该能够浏览到[http://nextamazing.site/](http://nextamazing.site/)并看到你完成的安装。

不喜欢用`-target`？是的，很糟糕；

> 这种定位功能是为特殊情况提供的，例如从错误中恢复或解决地形限制。不建议*使用`-target`进行常规操作，因为这可能会导致未被发现的配置漂移，并混淆资源的真实状态与配置的关系。*

 *请看下面关于如何在现实生活中实现这一点的建议。您真的不应该在生产使用中采用这种方法。

## 更多设计决策

在被视为“生产就绪”之前，还需要做出一些设计决策

*   该站点应该真正在 HTTPS 上运行，无论这是通过 AWS 管理的证书(通过 ACM)还是通过 ACM 或 IAM 提供给负载平衡器的外部签名的 CSR 来完成的，
*   尽管通过 CloudFront 交付资产的基础设施已经就绪，但它并没有作为 Terraform 运行的一部分在 WordPress 中设置。有几个选项，免费的和付费的，将实现这一点，即插件或自定义 cron 作业，
*   您将如何维护备份？目前，将使用 RDS 快照默认值。你将如何备份你的 WordPress 安装？

## 包装完毕...

暂时就这样了。这应该很好地介绍了如何使用 Terraform 在 AWS 上部署完整的解决方案。前面我提到了为这篇博客文章的目的所做的一些简化，一些需要考虑的事情；

*   本文是在 Terraform 0.12 发布之前开始的，应该考虑是否应该在 0.12 上开始一个新的项目，假设依赖关系兼容，
*   我在这里采用的主要创造性许可是创建一个大型模块，它需要以特定的顺序调用独立的组件来实现特定的最终结果。由于一些模块是相互依赖的(你没有理由不能运行两次`terraform apply`并成功部署)，我建议要么把它分解成不同的模块，即`base`、`seeder`和`wordpress`，要么使用像 Terragrunt 这样的工具，
*   IaC 领域的思想领袖之一 Gruntwork 开发了 Terragrunt 来改进您的 Terraform 工作流，以减少在生产中运行时的潜在问题。这里的一大优势是能够将 Terraform 状态(记录 Terraform 已部署的内容)划分为更小的块，以减少状态损坏或丢失时的影响(这是一种确定的可能性)。在像这样的大型多模块部署中，这个工具是值得考虑的。

### 推荐阅读

**Terraform 学习轨迹(HashiCorp)**

[https://learn.hashicorp.com/terraform/](https://learn.hashicorp.com/terraform/)

**Terragrunt 文档**

[https://github.com/gruntwork-io/terragrunt](https://github.com/gruntwork-io/terragrunt)

**地形状态如何出错**

[https://charity . wtf/2016/03/30/terra form-VPC-and-why-you-want-a-TF state-file-per-env/](https://fluffycloudsandlines.blog/deploying-ha-wordpress-on-aws-using-terraform-and-salt/charity.wtf/2016/03/30/terraform-vpc-and-why-you-want-a-tfstate-file-per-env/)*