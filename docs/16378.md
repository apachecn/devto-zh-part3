# 让 Inspec 和 SSH 在 Google 云平台上与 Packer 一起工作

> 原文：<https://dev.to/baens/making-inspec-with-ssh-work-with-packer-on-the-google-cloud-platform-16gm>

*这篇文章最初是在[blog.baens.net](https://blog.baens.net/posts/making-inspec-work-with-gcp-and-packer/)T3 发表在我的博客上的*

# 背景

这篇博文旨在帮助任何试图在[谷歌云平台](https://cloud.google.com/)上制作[打包机](https://www.packer.io/)图像的人，该图像已经过 [Inspec](https://www.inspec.io/) 的测试。我最近一直在日常生活中使用这些工具，我想让这三个工具一起工作，这是令人惊讶的困难。这篇博客文章希望能帮助那些想做同样事情的人。

我想从这些工具中得到的是让 Packer 启动一个新的虚拟机，运行所有的构建步骤，然后用 Inspec 测试那个虚拟机。此外，我不想因为安装了超出实际需要的东西而使虚拟机变得混乱，所以我不想让 Inspec 在新虚拟机上安装了所有工具的情况下运行。这意味着我希望 Inspec 从我的机器上运行，然后通过 SSH 连接到新机器。听起来很简单，对吗？好吧，有足够多的陷阱和搜索资源的缺乏，我觉得我需要写下来。

废话少说，让我们看看代码是什么样子的。如果你想看到所有的东西放在一起，[代码库在这里](https://github.com/baens/code-examples-blog.baens.net/tree/packer-gcp-ssh-key)。

# 我面临的问题

因此，让我来介绍一下我在这个旅程中遇到的问题，我将尝试并展示这些问题的答案。

问题#1:我遇到的第一个问题是，我需要 Inspec 与新的 VM 对话，但是 Packer 不太容易提供这些变量。我需要的是运行在云中的虚拟机的主机 IP。因为 Packer 没有提供开箱即用的功能，所以我必须想办法绕过它。

问题 2:一旦我建立了连接，我就需要用 VM 认证 Inspec。Inspec 可以通过 SSH 进行本地连接，因此创建和建立一个 SSH 密钥来登录是显而易见的选择。然而，Packer 还是没有提供一个方便的方法来做到这一点，所以我需要自己建立那个键，并手动设置一些东西来使它工作。

# 解决问题 1:获取 GCP 打包虚拟机的 IP

这听起来很简单，但却非常困难:我如何获得 Packer 正在与之通信的当前正在运行的虚拟机的 IP 地址？一种方法可能是 ping 一些告诉你你的公共 IP 的远程网站，但是引入另一个你控制之外的层似乎是荒谬的。我的搜索偶然发现了一些如何在 AWS 上做到这一点的例子，但在 GCP 上却很少。然而，使用 AWS 的人确实给了我一个做什么的想法，或者至少是搜索什么。

人们在 AWS 上经常使用的东西叫做元数据服务器。元数据服务器是运行在 [AWS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) (和 [GCP](https://cloud.google.com/compute/docs/storing-retrieving-metadata) )上的每一个虚拟机的服务，它可以告诉你你正在运行的实例。这正是我需要的东西，我找到了 GCP 的[文档，果然，里面有所有暴露的 IP 地址的信息。我需要的特定地址位于这个 URL: `http://metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip`。更好的是，这是跨实例通用的，所以我不必根据机器做一些复杂的逻辑。](https://cloud.google.com/compute/docs/storing-retrieving-metadata)

现在我有了可以获取外部 IP 地址的 HTTP 地址，我该如何从命令行中准确地检索这些信息呢？谢天谢地，curl 已经安装好了，所以我编写了这个命令:

```
curl  -H \"Metadata-Flavor: Google\" metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip 
```

执行了那个和 viola，我需要的 IP 地址。

现在，需要指出的是。我们添加的 HTTP 头(`Metadata-Flavor: Google`)是必需的。这是一个安全的守卫来保护你，以防你不小心点错了这个位置。

现在我有了我的 IP 地址，我该如何使用它呢？同样，长时间的搜索没有显示出从一个打包步骤到另一个打包步骤的管道变量的方法，那么我该怎么办呢？我不是 100%确定我是如何偶然发现的，但我发现了一个非常巧妙的方法，在你创建文件后下载它。以下是打包程序的供应步骤:

```
"provisioners":  [  {  "type":  "shell",  "inline":  ["curl -H \"Metadata-Flavor: Google\" metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip > /tmp/ip"]  },  {  "type":  "file",  "direction":  "download",  "source":  "/tmp/ip",  "destination":  "./host"  }  ] 
```

这就解决了问题。只需创建一个包含来自特殊内部端点的数据的文件，然后下载该文件供后续步骤使用。轻松点。或者至少一旦你解决了就简单了。真不敢相信

# 解决问题 2:通过 SSH 进行通信

现在我有了 IP 地址，我需要从 Inpsec 与该 IP 进行通信。Inspec 有几种与机器通信的方式。它可以在本地执行，甚至可以在远程机器上执行。我想在本地执行，但在远程机器上通信，这样我就不必在远程机器上安装任何东西。这引导我找到了使用 SSH 与远程机器通信的途径。默认情况下，SSH 需要用户名和密码，因为我将在自动化构建环境中使用它。让系统提示输入用户名和密码是行不通的，更不用说这也不太安全了。因此，我需要在自动化过程中设置一个 SSH 密钥，以便登录到远程机器。

[Google cloud 提供了一种方法，让你提供一个 SSH 密钥](https://cloud.google.com/compute/docs/instances/adding-removing-ssh-keys#edit-ssh-metadata)，它将被安装在机器上供你使用。首先让我们生成密钥。Google 解析关键字信息的方式是，它在关键字内寻找与此相关的用户的评论。下面是我用来生成密钥的命令:

```
ssh-keygen -f inspec-key -C packer -N '' -m PEM 
```

该命令将输出一组名为`inspec-key`的 SSH 密钥文件，带有注释`packer`，并且`-N`标志是要使用的密码(在本例中为空)。您需要指定 PEM 格式，因为一些将要加载的 Ruby 模块不能解析新的 OpenSSH 密钥格式。

好了，我现在有了 SSH 密钥，现在我需要放置 SSH 密钥，以便 Packer 在创建 VM 实例时使用。为此，您需要用几个参数修改构建器。下面是构建器变成的样子:

```
"builders":  [  {  "type":  "googlecompute",  ...  "metadata":  {  "ssh-keys":  "packer:{{user `inspec-key`}}"  }  }  ], 
```

下面是运行 Packer 的命令:

```
packer build -var inspec-key=$(cat inspec-key.pub) image.json 
```

`metadata/ssh-keys`是重要的部分。添加带有用户名的元数据，然后是密钥的内容。这一开始让我犯了个错误，因为我以为已经用所有相关信息设置好了密钥，但是反复试验找到了正确的格式。而且明确一下，格式是`<username>:<ssh public key data>`。

好了，现在我们有了新的实例，我们如何连接 Inspec？同样，我真的不想安装另一个工具，所以让我们使用 docker 来运行实际的工具。docker 命令将如下所示:

```
docker run --rm -v $(pwd):/workspace -w /workspace chef/inspec:3.2.7 detect -t ssh://packer@$( cat host ) -i inspec-key 
```

这里需要解释的事情很少:

*   `-v $(pwd):/workspace -w /workspace` -获取当前目录并在`/workspace`挂载它，当 docker 容器在当前目录中运行时，创建当前目录。
*   `chef/inspec:3.2.7 detect`-docker 图像和运行的命令。在演示中，我使用了`detect`来展示它的连接。当你真正想要运行测试时，你运行`exec`。
*   这里有一些神奇的东西。告诉 Inspect 通过`ssh`连接我们在上一步下载的`host`文件中的数据。接下来，使用我们之前创建的`inspec-key`文件。

# 由此产生的打包文件和测试运行

好了，所有这些看起来会像这样:

```
{  "builders":  [  {  "type":  "googlecompute",  "project_id":  "{{user `gcp-project`}}",  "source_image_family":  "ubuntu-1804-lts",  "zone":  "us-central1-a",  "image_description":  "Image demo for SSH keys",  "ssh_username":  "packer",  "tags":  "packer",  "image_name":  "test-image-{{isotime | clean_image_name}}",  "metadata":  {  "enable-oslogin":  "false",  "ssh-keys":  "packer:{{user `inspec-key`}}"  }  }  ],  "provisioners":  [  {  "type":  "shell",  "inline":  ["curl metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip -H \"Metadata-Flavor: Google\" > /tmp/ip"]  },  {  "type":  "file",  "direction":  "download",  "source":  "/tmp/ip",  "destination":  "./host"  },  {  "type":  "shell-local",  "command":  "docker run --rm -v $(pwd):/workspace -w /workspace chef/inspec:3.2.7 detect -t ssh://packer@$( cat host ) -i inspec-key"  }  ]  } 
```

构建器已经用我们的密钥设置好了，以便我们进行连接。然后，该工具下载服务器的 IP，我们使用 Inspec 连接到主机。它运行时应该是这样的:

[![Demo of the build](img/ebc171cca0674ab193930fd6fd776c86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UiIn9ByH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.baens.nimg/cloud-build.gif)

[链接到构建的 gif，开发到](https://blog.baens.nimg/cloud-build.gif)时出现了问题

如果你想自己运行这个，所有的源代码都可以在 github 上找到。