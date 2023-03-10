# 使用包装材料和盐制作灯 AMI

> 原文：<https://dev.to/tomwerneruk/creating-a-lamp-ami-using-packer-and-salt-mdb>

这是一系列文章中的第一篇，创建一个基础设施，作为运行在 Amazon Web Services 上的 WordPress 的代码驱动部署。

这一个将会是相当短的，部分由于伟大的工具提供！

## 什么创造一个形象？为什么是帕克？为什么是盐？

在 DevOps 环境中，创建尽可能多的安装和配置映像是至关重要的，可预测性和敏捷性是关键。例如，如果你有一个创建 WordPress 应用服务器池的自动缩放组，使用部署脚本安装 Apache、PHP 和 MySQL 客户端将意味着一个节点需要很长时间才能进入服务。而准备定制的 AMI 将意味着进入服务的时间仅受启动 EC2 实例的时间长度的限制。

Packer 是更广泛的 Hashicorp 工具集的一部分，用于通过 IaC 控制云。Packer 可以为广泛的云和内部平台创建映像。作为同一个工具家族的一部分，它们的工作方式和您将要编写的基础设施代码有一定程度的相似性。

Salt 是市场上几种配置工具之一。在学习了木偶、安斯比尔和盐之后，我对其他任何工具都没有真正的忠诚。绿地环境非常罕见，因此您可能会受到当前工具集的限制。这里使用的概念可以移植到其他配置工具上，Packer 支持许多配置工具！

## 打包机和盐快速入门

Packer 非常容易上手，就像 Hashicorp 的其他产品一样，只需下载、解压并运行即可。前往我的 YouTube 频道观看我的视频。

从[https://git lab . com/fluffy-clouds-and-lines/packer-and-salt-lamp-ami](https://gitlab.com/fluffy-clouds-and-lines/packer-and-salt-lamp-ami)中克隆教程 repo。你应该有一个这样的结构:

```
├── aws_vars.json
├── README.md
├── salt_tree
│   └── srv
│   ├── pillar
│   │   ├── apache.sls
│   │   ├── mysql.sls
│   │   └── top.sls
│   └── salt
│   ├── apache
│   ├── mysql
│   ├── php
│   └── top.sls
└── template.json 
```

**aws_vars.json**

这用于提供可变数据，以避免每次调用时都必须指定它

**盐 _ 树**

包含安装和配置 are 灯堆栈组件的 Salt 声明。这在构建期间由 Packer 复制到远程主机，然后由 Salt 执行。

**template.json**

Packer 构建声明，指定要使用的基本 AMI，以及在构建过程中如何调用 Salt。

## 封隔器运行

下封隔器非常简单。在克隆 repo 之后，您需要决定的是是否将您的凭证放入一个文件中。

要进行提示(或使用您的 AWS CLI 凭据，如果已配置)，请从项目根目录运行。

```
packer build template.json 
```

或者，如果你已经创建了一个变量文件；

```
packer build -var-file=./aws_vars.json template.json 
```

构建大约需要 5 分钟，以创建基本实例、应用 Salt 配置并生成最终 AMI。

## 在 template.json 里面

封隔器模板有 3 个主要部分(不包括后处理器，后处理器是可选的，用于特定用例)；

**变量**

用于从代码中提取应该提供给脚本的敏感或动态信息。

**建筑商**

创建一台具有适当基础映像的机器，以开始构建，然后进行配置、打包以备使用。

**置备程序**

使用脚本、配置管理工具(Chef、Puppet、Ansible、Salt 等)将变更实际应用到构建中的方法。

查看包装文档，获取最新的可用建筑商和供应商列表。

```
{
  "variables": {
    "aws_access_key": "",
    "aws_secret_key": ""
  }
...
} 
```

我们的变量是声明的，所以它们可以在模板的后续部分使用。它们可以在运行时提供，JSON 文件，环境变量，Consul 或者 Vault(酷吧？).

```
{
...
  "builders": [
    {
      "type": "amazon-ebs",
      "access_key": "{{user `aws_access_key`}}",
      "secret_key": "{{user `aws_secret_key`}}",
      "region": "eu-west-2",
      "source_ami_filter": {
        "filters": {
          "virtualization-type": "hvm",
          "name": "*ubuntu-bionic-18.04*",
          "root-device-type": "ebs"
        },
        "owners": [
          "099720109477"
        ],
        "most_recent": true
      },
      "instance_type": "t2.micro",
      "ssh_username": "ubuntu",
      "ami_name": "wordpress-ha-node {{timestamp}}"
    }
  ],
 ...
} 
```

我们的构建者指定我们想要一个 EBS 支持的 AMI，基于最新的 Ubuntu 18.04 镜像。

```
{
...
  "provisioners": [
    {
      "type": "salt-masterless",
      "local_state_tree": "./salt_tree/srv/salt",
      "local_pillar_roots": "./salt_tree/srv/pillar",
      "salt_call_args": "pillar='{\"role\":\"builder\"}'"
    },
    {
      "type": "shell",
      "inline": [
        "rm -rf /srv/salt",
        "rm -rf /srv/pillar"
      ],
      "execute_command": "sudo sh -c '{{ .Vars }} {{ .Path }}'"
    }
  ]
} 
```

我们在这里使用两个置备程序(每个构建可以有多个)，它们按顺序运行。第一个置备程序以无主模式(无中央服务器)使用 Salt，将预定义的 Salt 树上传到主机并应用它。第二个置备程序是解决 Terraform 错误 [#20323](https://github.com/hashicorp/terraform/issues/20323) ，该错误阻止 Terraform 针对此映像重新运行 Salt 无主置备程序。

## 盐树内部

我发现盐是一种很难学的东西，在某些情况下它很容易理解，但是一些术语也需要时间来适应。这并不是一个完整的 Salt 介绍，而是更多地解释这个项目所使用的 Salt 定义。

Salt 是一种配置管理工具，它获取配置文件并使用它们将所需状态应用到系统。高级 Salt 使用状态来定义如何应用配置，使用支柱来提供可变数据。这类似于 Packer 有一个模板和一个单独的变量文件。

`/srv/salt/top.sls`是这里演出的领导。它定义了哪些状态适用于任何给定的 Salt 机器。

```
base:
  'role:builder': 
    - match: pillar # Match on 'role' passed in as additional Pillar data via salt_call_args
    - php
    - php.mysql
    - php.mysqlnd
    - apache
    - apache.config
    - apache.vhosts.standard
    - mysql # We don't need MySQL Server (using RDS instead), but can't be removed presently due to bug
    - mysql.config
    - mysql.client 
```

`top.sls`用在我们的树上；

*   将 PHP、Apache 和 MySQL 状态应用到主机。每个对应的文件夹都是一个预构建的盐状态，称为盐公式。所有这些都来自 https://github.com/saltstack-formulas。
*   `'role:builder'`是一个过滤器，用于决定哪些主机也要应用状态。在我们的下一个教程中，您将看到我们如何基于角色将同一棵树用于不同的目的。
*   每个列表项，即`php`或`mysql.client`是盐树中的一个文件夹。句点标记子文件夹。像这样拆分更大的公式是很常见的。

`/srv/pillar/top.sls`是我们的支柱配置根。这个；

*   定义由`/srv/salt/top.sls`应用的状态配置。
*   一些 Salt 公式具有合理的默认值，因此不会有相应的支柱条目。

您会注意到，在我们的 Packer Provisioner 块中，我们为`salt_call_args`提供了一个值`"pillar='{"role":"builder"}'"`。这提供了补充的支柱信息，可用于决定应用`/srv/salt/top.sls`的哪些部分。围绕这一点有很大的灵活性，还有其他方法来过滤这个文件。

我当然不是这里的盐专家。我在这里使用了预先构建的 Salt 公式，并将它们连接在一起以创建我想要的设置。

## 包装完毕

如果你已经到了这一步，你应该希望有一个非常快速的介绍包装和盐，并成功地建立一个形象，就像这样；

```
amazon-ebs: -------------
    amazon-ebs: Succeeded: 27 (changed=17)
    amazon-ebs: Failed: 0
    amazon-ebs: -------------
    amazon-ebs: Total states run: 27
    amazon-ebs: Total run time: 69.343 s
==> amazon-ebs: Provisioning with shell script: /tmp/packer-shell598436168
==> amazon-ebs: Stopping the source instance...
    amazon-ebs: Stopping instance
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating AMI wordpress-ha-node 1558710754 from instance i-08f3e01d313901737
    amazon-ebs: AMI: ami-0477fc2kjw982c28e81
==> amazon-ebs: Waiting for AMI to become ready...
==> amazon-ebs: Terminating the source AWS instance...
==> amazon-ebs: Cleaning up any extra volumes...
==> amazon-ebs: No volumes to clean up, skipping
==> amazon-ebs: Deleting temporary security group...
==> amazon-ebs: Deleting temporary keypair...
Build 'amazon-ebs' finished.

==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:
eu-west-2: ami-0477fc2kjw982c28e81 
```

基础设施编码快乐！欢迎下方评论和提问！