# Rust powered CLI apk 反编译程序

> 原文：<https://dev.to/robertohuertasm/rust-powered-cli-apk-decompiler-1h10>

最近我需要`reverse engineer`一些 [apks](https://www.androidpit.com/android-for-beginners-what-is-an-apk-file) ，我一直手动使用一些第三方工具来完成这些。但是，我们如何加快这一进程呢？一点点[锈](https://www.rust-lang.org/)会很有帮助！😉

## 铁锈对于建筑 CLI 来说是牛逼的

如果你没有意识到的话，Rust 也是构建 T2 CLI 工具 T3 的绝佳选择。为此它甚至有一个[专用页面](https://www.rust-lang.org/what/cli)！😉

看看这个**厉害的工具**:

1.  quicli ，
2.  [拍手](https://crates.io/crates/clap)，
3.  [结构图](https://crates.io/crates/structopt)，
4.  [控制台](https://crates.io/crates/console)...

## 进入 apk-反编译器

老实说，我们不喜欢一次又一次地做**重复无聊的任务**和经历 [dex2jar](https://github.com/pxb1988/dex2jar) 、 [apktool](https://ibotpeaches.github.io/Apktool/) 和 [Java 反编译器](https://github.com/kwart/jd-cmd)，这将不可避免地导致我们每个人**尝试自动化**这个过程。

这也正是为什么我构建了自己的 [CLI 实用程序](https://en.wikipedia.org/wiki/Command-line_interface)，它被戏称为 [apk-decompiler](https://github.com/robertohuertasm/apk-decompiler) 🎉。

## 如何获得 apk

你有几种方法可以做到这一点。

最简单的一种是浏览到 [ApkPure](https://apkpure.com) 并下载到你的电脑上。

更复杂的**一个**将需要你有一个`rooted`电话，并从那里拉`apk`。如果你对这个过程感兴趣，你可以读一下 [apk-decompiler](https://github.com/robertohuertasm/apk-decompiler) readme，里面有简明的细节。

## 如何使用

目前， [apk-decompiler](https://github.com/robertohuertasm/apk-decompiler) 只支持`MacOS`和`Linux`。

您可以从项目的[发布页面获得该软件并立即下载。](https://github.com/robertohuertasm/apk-decompiler/releases)

一旦你下载了它，你可以把它添加到你的*路径*中，并像这样使用它:

```
apk-decompiler <name-of-your-apk> 
```

Enter fullscreen mode Exit fullscreen mode

## 你会得到什么

您将获得一个名为`output`的新文件夹，其中包含以下文件夹:

1.  **反编译**:这是运行 [dex2jar](https://github.com/pxb1988/dex2jar) 的输出。
2.  **提取**:该文件夹包含`unzipping`到`apk`的输出。
3.  **xml** :这基本上是运行 [apktool](https://ibotpeaches.github.io/Apktool/) 的输出。
4.  **package-name-error.zip** :在反编译过程中出现错误的情况下，你将得到的可选文件。

## 已知问题

如果你的文件夹中有空格，这可能是这个项目的依赖项之一 jd-cli 的一个问题。因此...尽量避开他们就好！😜

## 链接到工具

万一你错过了 [apk-decompiler](https://github.com/robertohuertasm/apk-decompiler) 的链接，你可以在这里找到它[。](https://github.com/robertohuertasm/apk-decompiler)

请随意在 [Github 资源库](https://github.com/robertohuertasm/apk-decompiler)中改进、派生或提出问题。

尽情享受吧！

-
最初发布于[robertohuertas.com](https://robertohuertas.com/2019/02/03/rust_cli_apk_decompiler)2019 年 2 月 3 日。