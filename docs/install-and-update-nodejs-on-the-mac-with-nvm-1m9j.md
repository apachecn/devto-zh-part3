# 在装有 NVM 的 Mac 上安装和更新 NodeJS

> 原文：<https://dev.to/peterwitham/install-and-update-nodejs-on-the-mac-with-nvm-1m9j>

我想我会在我的网站上分享一个旧帖子，我发现自己在周末也指向一些人，当他们想没有麻烦地维护 NodeJS 版本时。

Nodejs 很棒，但我似乎总是以向人们解释如何安装它而告终。这就是我的方法，一劳永逸。

有 Windows 和 Mac 的安装程序，也有 Windows，Mac，Linux 和我相信 SunOS 的二进制文件。另外如果你想自己编译的话，还有源代码。

我个人喜欢用‘NVM’的方式，代表节点版本管理器。它不仅使事情变得简单，而且您还可以安装和切换到任意多个不同版本的 Node.js。

### 安装 nvm

我是在 Mac 上这么做的，但是一旦你安装了 nvm，它在所有平台上都差不多。所以现在就去[https://github.com/creationix/nvm](https://github.com/creationix/nvm)安装吧。因为我是在 Mac 上做的，所以我已经确保我满足了为 c++编译器安装 Xcode 的要求。

我将打开一个终端，按照说明运行安装脚本。值得注意的是，如果您已经安装了 nvm，您可以使用相同的命令更新它。

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```

### 安装节点

现在转到 https://nodejs.org，我看到它告诉我最新的稳定版本是 LTS 的 v10.15.0。我看到有一个 v11.8 电流与所有最新的功能，但我今天不使用那个。

所以我简单地使用终端和 nvm 来安装它

```
nvm install 10.15.0
```

我将把我想要使用的当前版本设置为相同的版本

```
nvm use 10.15.0
```

因为我想随着时间的推移使用这个版本(以防我安装了多个版本)，所以我也将它设置为默认值。

```
nvm alias default 10.15.0
```

我的朋友就是一切！类型

```
node —version
```

瞧，它告诉我我拥有的版本是

v10.15.0

我们完成了，现在坐下来笑或者写一些代码！