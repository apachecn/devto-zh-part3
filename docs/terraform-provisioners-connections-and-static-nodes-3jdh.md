# 地形、供应器、连接和静态节点

> 原文：<https://dev.to/adron/terraform-provisioners-connections-and-static-nodes-3jdh>

**注意:** *视频在帖子底部，带有时间轴，这样你就可以导航到视频的某些点，我在那里谈论不同的话题。*

## 当前群集节点

首先，我从我前一天晚上遇到的一个简单问题开始这次会议。事实上，正在创建的实例资源名称中的变量会导致在每次执行 terraform 时销毁和创建实例。这样做不行，我只需要声明每个节点，至少前三个，也许五个(因为在大多数分布式系统中最好是 2n+1 个实例)。因此，我删除了在上一个会话中创建的模块，并创建了两个节点，用于创建和设置我将用来设置 Cassandra 的 provisioners。

除了静态声明每个初始节点之外，我还添加了公共 IP，至少现在是为了故障排除。设置完成后，我可能会删除它们，并确保群集仅在私有 IP 上通信，或者至少不允许流量通过防火墙设置到达群集的单个节点，即使是通过公共 IP。

节点，公共和私有 IP 地址，现在看起来像这样。

```
resource  "google_compute_address"  "node_zero_address"  {  name  =  "node-0-address"  subnetwork  =  "${module.network_development.subnetwork_west}"  address_type  =  "INTERNAL"  address  =  "10.1.0.2"  }  resource  "google_compute_address"  "node_zero_public_address"  {  name  =  "node-0-public"  }  resource  "google_compute_instance"  "node_zero"  {  name  =  "node-0"  machine_type  =  "n1-standard-1"  zone  =  "us-west1-a"  boot_disk  {  initialize_params  {  image  =  "ubuntu-minimal-1804-bionic-v20180814"  }  }  network_interface  {  subnetwork  =  "${module.network_development.subnetwork_west}"  address  =  "${google_compute_address.node_zero_address.address}"  access_config  {  nat_ip  =  "${google_compute_address.node_zero_public_address.address}"  }  }  service_account  {  scopes  =  [  "userinfo-email",  "compute-ro",  "storage-ro",  ]  }  } 
```

## 供给商

当使用[地形供应器](https://www.terraform.io/docs/provisioners/index.html)时，有一些事情需要考虑。首先，很明显，但我仍然要提到的是，要知道哪个用户、ssh 密钥和相关的身份验证细节，您将使用它们来实际登录到您创建的实例。有时可能会有点混乱，至少不止一次，当 gcloud 开始为我创建 ssh 密钥时，我已经创建了自己的密钥。然后，当我设置 provisioner 时，我经常忘记我不想使用 id_rsa，而是使用 google 创建的密钥。我在视频中对此进行了更多的讨论，并提出了更多的想法来确保机器上的用户是正确的用户，它是为 centos 或 ubuntu 或您使用的任何发行版设置的，以及所有这些细节。

一旦这些都解决了，分类了，并且没有混淆，就只需要几个步骤就可以设置和运行 provisioner 了。首先，我们需要设置我们想要使用的置备程序。有几种选择；file、local-exec、remote-exec 和其他一些用于 chef、salt-masterless、null_resource 和 habitat 的文件。在这里，我将介绍文件、本地执行和远程执行。首先，让我们看看文件资源调配器。

```
resource  "google_compute_instance"  "someserver"  {  #  ...  some  other  fields  for  the  particular  instance  being  created.  #  Copies  the  myapp.conf  file  to  /etc/myapp.conf  provisioner  "file"  {  source  =  "directory/relative/to/repo/path/thefile.json"  destination  =  "/the/path/that/will/be/created/andthefile.json"  }  } 
```

这个 provisioner 的例子展示了几个关键的东西，特别是我的奇特命名；

*   置备程序作为实例资源本身中的一个字段进入实例资源。
*   我在这里选择了“文件”类型的资源调配器，该资源调配器在字段中有几个关键字段，对于文件资源调配器，这两个必需字段是源和目标。
*   源获取要复制到实例的文件，基于一般的路径和文件名，该实例位于这个存储库中的本地，或者我认为可能在它的外部。你可以把文件名放在，正在执行的根目录下。
*   目标是远程服务器上实例用户文件夹中的位置。因此，如果我设置一个文件夹/filename.json，它将创建一个名为“folder”的文件夹，并将文件放入其中，如果文件名不同，可能会重命名为 filename.json。

但是当然，没有用户和相应的身份验证，不能简单地将文件复制到远程实例，这就是连接资源在 provisioner 资源中发挥作用的地方。这使得置备程序看起来像这样。

```
resource  "google_compute_instance"  "someserver"  {  #  ...  some  other  fields  for  the  particular  instance  being  created.  #  Copies  the  myapp.conf  file  to  /etc/myapp.conf  provisioner  "file"  {  source  =  "directory/relative/to/repo/path/thefile.json"  destination  =  "/the/path/that/will/be/created/andthefile.json"  connection  {  type  =  "ssh"  user  =  "useraccount"  private_key  =  "${file("~/.ssh/id_rsa")}"  agent  =  false  timeout  =  "30s"  }  }  } 
```

这里，连接资源添加了几个用于建立连接的新字段。这与文件或远程执行置备程序相同。如前所述，通过视频中的故障排除，在这种情况下，我只是加入了语法 id_rsa，因为这是常见的 ssh 私有密钥。然而，对于 GCP，我需要使用 google_compute_engine ssh 私钥。

其他字段很容易理解，但是让我们快速讨论一下。类型，简单地说，就是将要使用的 ssh 连接。也可以使用纯文本和一些其他选项，或者在 windows 中使用 winrm 连接类型。

用户字段只是用户的名称，用于在创建远程实例时对其进行身份验证。私有密钥是用户的私有 ssh 密钥，该用户需要连接到该特定实例并执行置备程序将要执行的任何操作。

如果设置为 false，代理不使用 ssh-agent，但如果设置为 true，则使用 ssh-agent。在 windows 上，唯一支持的 ssh 验证是 pageant。

那么超时字段是在 terraform 执行中抛出错误之前，置备程序等待执行响应的时间，单位为秒，如图所示。

所有这些都设置好之后，我创建了一个简单的脚本来安装 Cassandra，我把它叫做 install-c.sh。

```
#!/usr/bin/env bash

# Installing Cassandra 3.11.3

echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA
sudo apt-get install -Y cassandra 
```

现在脚本完成了，我可以使用文件供应器将它移动到服务器上。我在视频中做了，然后开始组装远程执行供应器。然而，我也搞砸了本地执行 provisioner，但后来意识到我已经让他们在我的脑海里转过来了。local-exec 实际上启动了一个在本地机器上执行的脚本，terraform 从本地机器开始执行，我需要 terraform 来执行刚刚创建的远程实例上的脚本。那个置备者看起来像这样。

```
provisioner  "remote-exec"  {  inline  =  [  "chmod +x install-c.sh",  "install-c.sh",  ]  connection  {  type  =  "ssh"  user  =  "adron"  private_key  =  "${file("~/.ssh/google_compute_engine")}"  agent  =  false  timeout  =  "30s"  }  } 
```

在这个 provisioner 中，inline 字段接受将在远程服务器上执行的字符串数组。我可以在这里输入安装 cassandra 的安装步骤，但是考虑到我实际上想要采取额外的步骤和动作，这将使 privisioner 非常混乱。因此，我在这里做的是设置连接，然后为内联字段修改文件，以便它可以被执行，然后在服务器上执行脚本。然后，它会经历添加 apt-get repo、执行相应的验证步骤、运行和更新以及安装 cassandra 的步骤。

之后，我将结束本次会议。创建了静态实例，提供了专用的提供程序，并为安装了 cassandra 的提供程序建立和验证了连接。因此，对于下一个会话，我已经准备好开始将节点连接到一个群集，并采取我需要的任何后续步骤。

[https://www.youtube.com/embed/aT7cTR7_5SY](https://www.youtube.com/embed/aT7cTR7_5SY)

*   [0:57](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=57s) 颠簸代码简介
*   [3:43](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=223s) 第一集介绍，步入课程的平台。
*   [4:25](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=265s) 开始在课程中做笔记！
*   [6:12](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=372s) 描述了资源关键值中的变量导致资源重新创建每个“terraform apply”的情况，因此我分解了每个单独的节点。
*   [9:36](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=576s) 我开始使用 provisioner 在机器上获取一个脚本文件，并执行该脚本文件。这很棘手，因为人们必须弄清楚特定图像的用户是谁。还有一些奇怪的混乱与我的麦克风开始。解决这个问题，但是如果你知道 OBS 流媒体软件是怎么回事，为什么会这样，让我知道我洗耳恭听！:)
*   在让文件供应器最终工作，设置额外的超时字段等之后，我继续进行 IntelliJ 的下一步工作，因为 IDE 很棒。此外，HCL (HashiCorp 配置语言)的插件和相关元素(包括实际 IDE 中内置的功能)为 Terraform 工作提供了一些额外的自动完成功能。此时，我实际设置了 IDE(之前在使用 Visual Studio 代码时安装了它)。
*   特定于 Terraform 和手头工作的 IntelliJ 插件的设置。
*   我展示了 Terraform 中的自动完成功能实际显示的一些附加字段，这在故障排除或构建各种资源时很有帮助。
*   [37:05](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=2225s) 检查 Apache Cassandra(【http://cassandra.apache.org/】T2)下载和安装命令，并将这些命令添加到 Terraform provisioner 文件副本的安装脚本中，以复制到实例。
*   [43:18](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=2598s) 我通过 IntelliJ 为 terraform 构建/执行添加了一些配置。有特定于 terraform 的构建选项，也有 bash 文件执行选项，我在视频中介绍过。
*   [45:32](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=2732s) 我查看了 IntelliJ 设置，以确定在哪里指定 Terraform 可执行文件。这对于让其他 terraform 构建配置选项工作很重要。
*   [48:00](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=2880s) 从现在到...
*   [1:07:16](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=4036s) ...使用置备程序在节点上成功完成 Cassandra 安装。
*   [1:07:18](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=4038s) 通过将最新的变更提交到存储库来结束。
*   [1:09:04](https://www.youtube.com/watch?v=aT7cTR7_5SY&t=4144s) 黑客 outtro！

**资源:**

*   [地形文件](https://www.terraform.io/docs)
*   github Repo[terra formed-fields](https://github.com/Adron/terraformed-fields)
*   阿帕奇·卡珊德拉

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！