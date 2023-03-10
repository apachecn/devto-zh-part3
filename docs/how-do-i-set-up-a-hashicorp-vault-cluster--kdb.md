# 如何在 4 个命令行中设置本地 HashiCorp Vault 集群？

> 原文：<https://dev.to/v6/how-do-i-set-up-a-hashicorp-vault-cluster--kdb>

我想给你的是一个 Vault 环境，你可以在不到 [25 分钟](http://e.ggtimer.com/1500)内启动并运行它，只需 4 个命令行！*

*   免责声明:这 4 个命令行只有在安装了最新版本的 Git、Virtualbox 和 after 后才能工作。 <sup id="fnref1">[1](#fn1)</sup>

听起来怎么样？一套全面运行的 3 个 Vault 服务器，后端受 ACL 保护？

您以前是否只用 4 个命令行就做到了这一点？

如果没有，那么，好吧，我们走吧！

该图显示了“纯主机”网络中的 3 台虚拟机。这意味着，它们不是互联网的一部分，而是相当于你的笔记本电脑上的本地无线网络。你知道怎么有个人披萨吗？这是一个个人网络。你不需要担心这些东西，因为 VirtualBox 和 vagger 会为你做这些工作，我们会看到的。

图中由白色矩形表示的虚拟机名称是实例 5、实例 6 和实例 7。每个上面都安装了[执政官](https://www.consul.io/)和[金库](https://www.vaultproject.io)。Consuls 被配置为形成一个集群，该集群充当 Vault 服务器的存储和高可用性“后端”。

[![Vagrant setup with 3 VMs. Consul and Vault are installed on each VM. The Consuls are configured to form a cluster, which acts as the Vault backend.](img/a15b397d06300292b6474ae06c85c8ae.png "Vagrant setup with 3 VMs. Consul and Vault are installed on each VM.")](https://res.cloudinary.com/practicaldev/image/fetch/s--qct7SMbA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p3kk0l2oxzvumayi6b1g.png)

有意义吗？很好。如果没有，嘿，总有评论区。

首先，让我们弄清楚一些先决条件。

如果您被先决条件、要安装的工具或下载代码所困扰，请看看网上的资源，或者花几分钟在评论中发泄一下。

特别是对于流浪者，一旦你安装了流浪者和 Virtualbox，入门指南需要大约 30 分钟:[https://www.vagrantup.com/intro/getting-started/index.html](https://www.vagrantup.com/intro/getting-started/index.html)

但是，如果你发现一个 Vault 工作不正常的错误，请尽快在 [Vault 讨论组](https://groups.google.com/forum/#!forum/vault-tool)上发帖，因为他们可以帮助你判断这是不是一个 bug。

# 特定于操作系统的先决条件

*   MacOS: OSX 10.13 或更高版本
*   Windows: Windows 必须有 [Powershell 3.0](https://stackoverflow.com/a/32385347/2146138) 或更高版本。如果你用的是 Windows 7，我推荐 Windows Management Framework 4.0，因为它更容易安装

# 安装 VirtualBox 和 Git

首先要做的是 [Virtualbox](https://www.virtualbox.org/wiki/Downloads) 和 [Git](https://git-scm.com/downloads) ，因为它们将允许你运行一个“虚拟机”，并允许你分别获得这个演练的代码。

# 安装流浪汉

第一个工具是 vagger，一个帮助开发环境所有细节的程序。把它想象成代码的基础设施，但是对于虚拟机、容器和自动化设置程序的枯燥部分，你必须在它们上面运行来做开发工作。

### 安装工具

1.  确保你已经安装了 [Git](https://git-scm.com/downloads)
2.  安装*最新*版本的流浪者([注:WINDOWS 7 和 WINDOWS 8 需要 POWERSHELL > = 3](https://blogs.technet.microsoft.com/heyscriptingguy/2013/06/02/weekend-scripter-install-powershell-3-0-on-windows-7/) )
3.  安装*最新*版本的 VMWare 或 [Virtualbox](https://www.virtualbox.org/wiki/Downloads)

### 本文原因

当你等待所有这些东西安装的时候，这里有一个关于我为什么写这个的说明。

如果您搜索[,如何设置 hashicorp vault 集群？](https://duckduckgo.com/?q=how+do+I+set+up+a+hashicorp+vault+cluster)，您将获得一些涵盖概念的结果，一些讨论如何使用 Vault 的 API，一些讨论如何启用 Vault Enterprise 的一般功能。

例如，HashiCorp 有一个优秀的部署指南，如下所示:

[存储库部署指南](https://learn.hashicorp.com/vault/operations/ops-deployment-guide)

以及可靠的参考架构，如下所示:

[保险库参考架构](https://learn.hashicorp.com/vault/operations/ops-reference-architecture.html)

但是有一些先决条件。例如，在《部署指南》中，您首先需要知道如何安装 Vault 服务器，并知道如何在网络容器或其他基础设施上运行它，然后再开始阅读该指南。

参考体系结构更多的是对示例体系结构的概述，而不是深入到与 Vault 或 Consul 没有直接关系的命令的本质。

这些也不是关于使用哪种网络堆栈或其他基础设施来设置您的 Vault 集群的规定或意见。

我为那些我认为已经对脚本、Git、配置新的 SSH 连接、安装软件和虚拟机有所了解的人写了这篇指南，因为这些很难解释，而且在别处有更好的资源。

无论如何，这就是为什么我要写一个简单的 Vault 集群设置。安装几个跨平台的开源工具(vagger、VirtualBox 和 Git)，运行几个通用命令，等待 10 分钟，瞧，您的新 Vault 集群就可以运行了。

### 截屏示例

如果您想在浏览本指南时打开它，这里有一个设置的截屏示例:

[![Screencast of the Vagrant setup with 3 VMs. Consul and Vault are installed on each VM. The Consuls are configured to form a cluster, which acts as the Vault backend.](img/9ad3c342a8e7057f516782ae3497a978.png "Screencast of Vagrant setup with 3 VMs. Consul and Vault are installed on each VM.")](https://player.vimeo.com/video/310219248)

### 下载 Vault 集群设置的代码

*相关厂商文档链接:[https://help.github.com/articles/cloning-a-repository](https://help.github.com/articles/cloning-a-repository)T3】*

`git clone https://github.com/v6/super-duper-vault-train.git`

### 使用此代码制作一个 Vault 集群

*相关流浪商贩文档链接:[https://www.vagrantup.com/intro/index.html#why-vagrant-](https://www.vagrantup.com/intro/index.html#why-vagrant-)T3】*

1.  `cd super-duper-vault-train`
2.  `vagrant up` ##注意:您可能需要等待一段时间，等待`vagrant up`完成，并且在成功连接之前很长一段时间内会出现一些“连接重试”错误，因为虚拟机正在启动。确保你有最新的版本，并尝试流浪入门指南

> 注意:你已经走了这么远。现在是一个很好的时间找到你当地的菲尔茨或皮特或星巴克得到一个当之无愧的`<insert favorite snack or beverage>`，因为`vagrant up`过程...良好的...这需要一段时间。**让流浪者去做工作，而你得到额外津贴。**

1.  `vagrant status`
2.  `vagrant ssh instance5`在你`ssh`到那个名为`instance5`的虚拟机后，你会看到你的命令提示符变成显示`vagrant@instance5`。您还可以将`vagrant ssh`中的其他虚拟机列在`vagrant status`的输出中。
3.  现在，您可以在运行`vagrant ssh`的虚拟机中使用 Vault 或 Consul。例如，在您的任何虚拟机中尝试命令`consul members`或`vault status`。

# 跳马

### 探索金库集群

`ps -ef | grep vault` ##检查 Vault 进程(在被管理的实例中运行)

`ps -ef | grep consul` ##检查 Consul 进程(在被管理的实例中运行)

`vault version` ##输出应该是`Vault v0.10.2 ('3ee0802ed08cb7f4046c2151ec4671a076b76166')`

`consul version`。# #输出应该显示 Consul 代理版本和 Raft 协议版本

流浪汉博森有以下 IP 地址:

```
192.168.13.35

192.168.13.36

192.168.13.37 
```

Enter fullscreen mode Exit fullscreen mode

金库和执政官都在运行。

保险库在 8200 端口。

领事在 8500 端口。

### 打开标签页中的链接

http://192.168.13.35:8200 (金库)

http://192 . 168 . 13 . 35:8500(领事)

http://192.168.13.36:8200 (金库)

http://192 . 168 . 13 . 36:8500(领事)

http://192.168.13.37:8200 (金库)

http://192 . 168 . 13 . 37:8500(领事)

### 启动金库数据

*相关厂商文档链接:[https://www.vaultproject.io/api/system/init.html](https://www.vaultproject.io/api/system/init.html)T3】*

启动保险库。

在一个被管理的虚拟机上运行这个`curl`命令，或者在你安装了`curl`的计算机上运行这个命令。

```
 curl -s --request PUT -d '{"secret_shares": 3,"secret_threshold": 2}' http://192.168.13.35:8200/v1/sys/init 
```

Enter fullscreen mode Exit fullscreen mode

### 解封金库

*相关厂商文档链接:[https://www.vaultproject.io/api/system/unseal.html](https://www.vaultproject.io/api/system/unseal.html)T3】*

如果成功，该过程将在`192.168.13.35:8200`解封保险库。您可以对`192.168.13.36:8200`和`192.168.13.37:8200`使用相同的过程。

1.  使用您的解封密钥替换密钥`abcd1430890...`的值，并在被管理的虚拟机上运行它。

```
 curl --request PUT --data '{"key":"abcd12345678..."}' http://192.168.13.35:8200/v1/sys/unseal 
```

Enter fullscreen mode Exit fullscreen mode

1.  再次运行`curl`命令。但是对`"key":`使用不同的值。从运行`v1/sys/init`端点时收到的密钥中，用不同于上一步中使用的密钥替换`efgh2541901...`。

```
 curl --request PUT --data '{"key":"efgh910111213..."}' http://192.168.13.35:8200/v1/sys/unseal 
```

Enter fullscreen mode Exit fullscreen mode

# 非流浪者

请参考我们之前克隆的存储库中的文件[PRODUCTION _ installation . MD](https://github.com/v6/super-duper-vault-train/blob/develop/PRODUCTION_INSTALLATION.md)。

# 整理存储库策略和配置

要通过其 API 提供 Vault，请参考我们之前克隆的存储库中的 [`provision_vault`文件夹](https://github.com/v6/super-duper-vault-train/tree/develop/provision_vault)。

它有数据和脚本。

`data`文件夹的树对应于 HashiCorp Vault API
端点，如下所示:

[https://www . hashi corp . com/blog/coding-vault-policies-and-configuration # layout-and-design](https://www.hashicorp.com/blog/codifying-vault-policies-and-configuration#layout-and-design)

在
初始化并解封保险库之后，
通过其 API 快速配置保险库，您可以使用您初始根令牌的编码保险库
策略和配置
。

的。每个文件夹
中的 json 文件对应于通过其 API 发送到 Vault
的有效载荷，但为了方便起见，也可能有`.hcl`、
、`.sample`、`.sh`文件。

手动设置的进一步阅读:

[https://medium . com/rigged-ops/building-a-local-hashi corp-vault-cluster-5575 Fe 322 a 17](https://medium.com/rigged-ops/building-a-local-hashicorp-vault-cluster-5575fe322a17)

* * *

1.  *湿滑，版权所有，即使是矛盾的。刀刃非常锋利。这个项目可能包含自由/开源软件代码。未经授权使用或复制本网站上的材料是严格要求，并受到祝贺。不要吃。推荐使用🥽护目镜。优惠在参与地点有效，在这种情况下是指所有地点。旨在与其他报价相结合或以某种方式巧妙复制。每次访问限制一个流浪者。现金价值是一个比特币的 1/100000000，但不要逼我这么做，因为现在谁知道这意味着什么。这是细则，你为什么还在看这个？真的，这是越来越愚蠢，去代码。* [↩](#fnref1)