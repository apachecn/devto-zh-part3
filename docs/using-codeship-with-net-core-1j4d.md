# 使用代码与。网络核心

> 原文：<https://dev.to/ianknighton/using-codeship-with-net-core-1j4d>

在寻求免费贴纸的过程中，我今天早上尝试了一下 T2 代码。

也许有一天我会对这个应用程序做一个全面的回顾，但是粗略地看一下，这很酷。它们提供了一个选项，你可以只输入一组命令并让它们运行，而不是被迫到处使用`yaml`文件。

有了这个平台，我就可以用 GitHub 中的一个简单的回购来构建它，而无需考虑容器。

下面是我使用的脚本:

```
wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo add-apt-repository universe
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install -y dotnet-sdk-2.2
dotnet build 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，这会拉进。Net SDK 并安装它，这样您就可以运行像`dotnet build`和/或`dotnet test`这样的命令。我不禁想象有更好的方法来解决这个问题，但是在几分钟内从没有管道到运行是非常酷的。

我不知道有多大的逃跑欲望。Net 核心应用程序，但是我想既然我没有马上看到解决方案，那就值得分享一下。

现在我只需要弄清楚如何更多地使用它，这样我就可以得到那些贴纸了...