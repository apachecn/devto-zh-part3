# 在 Raspberry Pi 上安装 Node.js

> 原文：<https://dev.to/vorillaz/install-nodejs-on-a-raspberry-pi-4hd5>

**这篇文章摘自我的[博客](https://vorillaz.com)，所以请务必查看更多最新内容**

如果你想开始使用 Node.js 和 Raspberry Pi 板进行开发，你可能会发现自己遇到了麻烦，尤其是如果你拥有 Raspberry Pi Zero 或 Zero W，问题是 ARMv6l 架构不再受 NodeSource 二进制文件的支持，因此用 Linux 包管理器`apt-get`安装 Node.js 是不可能的。

您可以随时下载二进制文件，将其加载到您的主板上并安装 Node.js，但在本教程中，我们将使用官方的 Node.js 发行版存储库并自动执行该过程。

## Node.js 发布

在官方的 [Node.js 发行库](https://nodejs.org/dist/)中，你可以找到官方版本以及不同架构的版本。我们对 ARMv6l 二进制文件感兴趣:例如，如果你想安装`v9.9.0`版本，你必须寻找相关的资源。
在我们的例子中，它将是`https://nodejs.org/dist/v9.0.0/node-v9.9.0-linux-armv6l.tar.gz`。

##将 Node.js
SSH 安装到您的板中，并执行以下命令

```
curl -o node-v9.9.0-linux-armv6l.tar.gz https://nodejs.org/dist/v9.9.0/node-v9.9.0-linux-armv6l.tar.gz 
```

上面的命令会将二进制文件下载到你的设备上，之后，我们会将内容解压缩为:

```
tar -xzf node-v9.9.0-linux-armv6l.tar.gz 
```

最后，我们将 Node.js 添加到正确的位置，并使用:
将其公开为全局可用的命令

```
sudo cp -r node-v9.9.0-linux-armv6l/* /usr/local/ 
```

复制的二进制文件现在暴露在您的`$PATH`中，您可以通过检查 NPM 和 Node.js 版本来测试一切是否如预期的那样工作:

```
~ node -v
└── v9.9.0
~ npm -v
└── 6.4.1 
```

为了将来更新的安全，你也必须安装 Git，因为 NPM 与它紧密耦合。您可以将 Git 安装为:

```
sudo apt-get install git 
```

## 最后的想法

您可以随时使用 [Balena](https://www.balena.io/docs/learn/getting-started/raspberry-pi/nodejs/) 来实现安装和未来升级的自动化。此外，还有一个可爱的、精心策划的[存储库](https://github.com/sdesalas/node-pi-zero)，其中包含用于 Raspberry Pi Zero 和 Raspberry Pi Zero W 设备的即插即用安装脚本。

## 购买树莓 Pi 板

可以从 [Seeed Studio](https://www.seeedstudio.com/Raspberry-Pi-4-Computer-Model-B-4GB-p-4077.html) 在线购买树莓 Pi 4。