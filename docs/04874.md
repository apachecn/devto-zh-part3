# 使用 S3FS 将 S3 挂载为文件系统

> 原文：<https://dev.to/jmoore53/mounting-s3-as-a-filesystem-with-s3fs-538i>

### S3FS

这个月，我花时间使用 AWS S3 桶和 S3FS 在我的开发机器和云之间创建了一个无缝的文件传输系统。我有一台 Mac 笔记本电脑和一台基于 Ubuntu 的台式机，它们的重要文件经常不同步。这些文件存在于版本控制之外，我一直在寻找一种比 Google Drive 或带有附件的电子邮件更好的方法来在机器之间传输它们。在做了一些快速的研究后，我发现 [S3FS](https://github.com/s3fs-fuse/s3fs-fuse) 是将 S3 挂载为文件系统的一种方式，并认为这是最好的工具。

### 轻松安装

S3FS 的开发者使得在基于 unix 的平台上安装这个工具变得非常容易，我使用
在我的 Mac 上安装它没有任何问题

```
brew cask install osxfuse
brew install s3fs 
```

Enter fullscreen mode Exit fullscreen mode

而在 Ubuntu 上用:

```
sudo apt-get install s3fs 
```

Enter fullscreen mode Exit fullscreen mode

### 国书& AWS

启动并运行该工具最复杂的部分是配置 aws 凭证和 S3 桶。

创建 bucket 非常简单，但是一定要在 bucket 上设置正确的设置，并给它一个符合 DNS 的名称。我把我的命名为 *itsltns* ，因为我知道它是 DNS 兼容的，而且很容易记住。

**为了说明这一点:**他们正在寻找的凭证是*访问密钥 ID* 和*访问密钥*。这些可以通过登录 AWS 并找到 [**我的安全凭证**](https://console.aws.amazon.com/iam/home?region=us-east-2#/security_credentials) 页面找到。这对密钥是开发人员在开发 AWS 应用程序时最常用的。请确保这些密钥或您的 IAM 用户具有正确的权限来访问 S3，我给了我的 IAM 用户完全的读写权限。

生成密钥对后，它们需要放在文件中任何你能找到它们的地方。使用`ACCESS_KEY_ID:ACCESS_KEY_SECRET`作为格式[建议](https://github.com/s3fs-fuse/s3fs-fuse#examples)把它们放在`~/.passwd-s3fs`中，但是我把它们和我的 AWS SSH 密钥放在`~/.ssh/AWS/passwd-s3fs`中。该文件需要拥有读写权限和组权限，而其他人没有权限。使用`chmod 600 ~/.ssh/AWS/passwd-s3fs`,我们能够运行 s3fs 命令并访问 S3 桶。

### 挂载点

为了保持简单和挂载点归单个用户所有，我选择在两台机器上的主目录下创建一个挂载点。我使用`mkdir ~/itsltns-s3`创建了这个挂载点，作为一个小小的铺垫，我还运行了命令`chmod a+rwx ~/itsltns-s3`,因为我在从 mac-to-ubuntu 和 ubuntu-to-mac 读写文件时遇到了读/写权限问题。

配置好挂载点后，我使用命令:
启动并运行

```
s3fs itsltns ~/itsltns-s3 -o passwd_file=${HOME}/.ssh/AWS/passwd-s3fs 
```

Enter fullscreen mode Exit fullscreen mode

这允许我运行一个基本的`ls ~/itsltns-s3`,它返回时什么也没有。为了测试挂载是否工作，我添加了一个`test.txt`文件，果然它被复制到了云中，而`test.txt`在`itsltns` s3 桶中。

### 发布&日志记录

如果你在挂载或`s3fs`上遇到任何问题，我强烈建议使用日志记录功能，并在最后加上`-o dbglevel=info -f -o curldbg`标志。添加调试选项后，该命令将类似于:

```
s3fs itsltns ~/itsltns-s3 -o passwd_file=${HOME}/.ssh/AWS/passwd-s3fs -o dbglevel=info -f -o curldbg 
```

Enter fullscreen mode Exit fullscreen mode

### MacOS - launchd -用户登录时挂载

我想要的特性之一是在登录时自动挂载 S3 驱动器，所以我选择创建一个在登录时运行的 [launchd](https://www.launchd.info/) 服务。

**使用 launchd 服务时**不要安装 launchd 网站上宣传的 LaunchControl，除非你打算进行严肃的代理或服务开发。该应用程序功能最少，需要许可证。

设置 launchd 并不可怕，但是我不得不使用`launchctl list | grep itsltns`调试服务几次，以获得应用程序的状态代码。

没有太多关于启动的细节，我创建了文件`~/Library/LaunchAgents/local.itsltns-s3.plist`作为我的启动服务，内容是:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <string>local.itsltns-s3.plist</string>
        <key>Program</key>
        <string>/Users/Jack/Library/LaunchAgents/itsltns-s3.sh</string>
        <key>RunAtLoad</key>
        <true/>
    </dict>
</plist> 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看`.plist` xml 文件，您将看到单词“Program”周围的`<key>`标记和将要执行的 shell 脚本位置周围的`<string>`标记。

这样，我也创建了文件`~/Library/LaunchAgents/itsltns-s3.sh`，其内容为:

```
#!/bin/bash
/usr/local/bin/s3fs itsltns /Users/Jack/itsltns-s3/ -o passwd_file=${HOME}/.ssh/aws/passwd-s3fs -o volname="ITSLTNS - S3" 
```

Enter fullscreen mode Exit fullscreen mode

`itsltns-s3.sh`脚本使用`s3fs`命令的绝对文件路径、挂载驱动器的位置和 s3fs 密码文件的位置。我没有费心在脚本中添加路径。

在上面的脚本中，您可能会注意到一个额外的部分，那就是 osxfuse 选项，该选项使用`-o volname=""`命令重命名连接的驱动器，这使得卷在 Finder 中看起来更漂亮(用所需的驱动器名替换引号之间的内容)。

随着附加服务和启动服务的创建，我的 Mac 已经安装完毕，可以启动了。我把它配置好了，一切都符合标准。

### Ubuntu - /etc/fstab -安装在开机上

配置 Ubuntu 来安装 S3 桶更具挑战性，我在这个过程中遇到了一些问题，但我真的只花了大约 20 分钟，一个快速的谷歌搜索和一次重启。

S3FS 提供了一个使用`/etc/fstab`文件自动挂载的例子，我最终得到了一个与他们的示例文件相似的配置。我的`/etc/fstab`文件底部加了`s3fs#mybucket /path/to/mountpoint fuse _netdev,allow_other 0 0`。

我运行了`sudo mount -a`，果然 S3FS 不能挂载，因为它[不知道我的开发者证书在哪里](https://github.com/s3fs-fuse/s3fs-fuse/issues/128)。`sudo mount -a`命令给出了类似“找不到访问和密钥”的响应。

`/etc/fstab`需要 root 权限(用`sudo`修改),因此它寻找不同的密码文件，所以我使用了一个符号链接来解决这个问题。使用`ln -s /home/jack/Documents/.ssh/aws/passwd-s3fs /etc/passwd-s3fs`是防止我做傻事的创可贴。

我重新跑了一遍`sudo mount -a`，果然它轻松地安装好了。

我重启了我的机器，一切似乎都正常。

### 定价

我已经好几个月没有运行它了，实际上我刚刚设置好，但是看起来所有的请求和存储价格大约是每月 2 美元。这是基于 16gb/月和 600，000 个总请求。

如果有必要的话，我会在需要的时候恢复到手动挂载，但是现在让 S3 在登录(mac)和启动(linux)时自动挂载还是不错的。

有关定价的更多信息，请参见 [AWS 定价计算器](https://calculator.s3.amazonaws.com/index.html)。

### 备注&待办事宜

*   MacOS 将属性和元数据附加到文件上，这有点糟糕。可以用`xattr -c filename`删除文件属性，但是我需要确保 bucket 中的每个文件都被删除了。原因是 Ubuntu 无法读取这些属性(不同的文件系统),它们也困扰着我。

*   看完这篇文章后，我需要将两台机器上的键移动到相似的位置。管理两台`~/Documents/.ssh/aws`和`~/.ssh/aws`有相似文件的计算机并不有趣。他们真的应该非此即彼。

*   我可能不得不修改 s3fs 代码，以允许多个 s3 挂载，因为我绝对认为这是一种在机器和 aws 帐户之间管理个人和工作文件的方式。我也可能会用一些自定义脚本来研究一下 [autofs](https://wiki.archlinux.org/index.php/autofs) 来解决这个问题。

*   可能需要查看不同机器上的挂载点之间的权限，但是现在我是唯一使用我的机器的人，所以没有安全威胁。*敲木头*

*   我还想在 Mac 上为启动脚本找一个位置。`~/Library/LaunchAgents`似乎是一个文件很容易被放置和丢失的地方。似乎是一个会很快变得一团糟的地方。