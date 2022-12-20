# 新的 Mac 设置

> 原文：<https://dev.to/jakedohm_34/new-mac-setup-5595>

几个月前，我购买了一台 2015 年的 27 英寸 iMac，作为我工作的主要开发机器。当我开始安装和配置我需要的所有应用程序和软件时，我觉得记录一切并与大家分享会很有趣！

请在评论中告诉我你们安装的应用程序会让我的生活变得更轻松！

我将分享以下几类设置:应用程序、开发应用程序、工作应用程序、Chrome 扩展、CLI 工具和配置。

## 应用

*   Chrome(日常浏览器)
*   Spotify
*   1 密码
*   Alfred App (+ Powerpack)
*   眼镜(窗口管理)
*   Dropbox
*   事物(任务管理)
*   观念(写作/组织)

## 开发应用程序

*   Github 桌面
*   Sequel Pro(数据库管理)
*   VS 代码(下面的扩展)
    *   钴 2 主题(韦斯博斯)
    *   进口成本
    *   fun—代码格式(Esben Petersen)
    *   vertu_pinewu)
    *   VS 实时分享
    *   树枝(什么东西)
    *   工艺 2 的树枝(塞尔文·奥尔蒂斯)
*   传输(FTP+)

## 工作申请单

*   收获应用程序
*   素描
*   Photoshop (Adobe CC)
*   Illustrator (Adobe CC)

## 镀铬扩展

*   1 密码
*   vue . js Dev tools 检视. js dev 工具
*   Adblock(和 Plus)
*   流行阻滞剂
*   隐私(令人敬畏的信用卡隐私工具)

## CLI 工具(按顺序)

*   公司自产自用
*   设计者
*   拉勒维尔代客(本地开发)
*   MariaDB
*   结节
*   故事
*   Xcode 命令行工具

## 配置

我喜欢 macOS 工作方式的几乎所有东西，所以我只需要配置一件事:默认情况下，截图以 PNG 格式保存，中等/大的截图通常最终在 2mb 左右(以我的经验)。我宁愿将截图保存为 jpg，因为它们要小得多，通常不到 1mb。

要更改此默认设置，您可以在“终端”中运行一个命令:

```
defaults write com.apple.screencapture type jpg 
```

一旦你改变了默认设置，你必须重新启动一点操作系统来使它生效:

```
killall SystemUIServer 
```

然后你就完成了，你的截图会自动保存为。jpg 文件。

## 终于

就是这样！我有目的地没有在我的个人电脑上安装一些应用程序，因为我打算把我的 iMac 专用于工作，所以我试图让它更难被转移😬