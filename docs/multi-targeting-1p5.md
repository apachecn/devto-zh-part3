# 多目标

> 原文：<https://dev.to/borrrden/multi-targeting-1p5>

Couchbase Lite。NET 在许多不同的地方运行，这意味着:

*   。Windows 上的. NET 框架
*   。Mac、Windows、Linux 上的 NET Core
*   Xamarin iOS
*   Xamarin Android
*   UWP

目前的结构是，整个库是一个. NET 标准 2.0 库，通过依赖注入提供一些特定于平台的特性。然而，这种方法有一些缺陷，所以我决定看看我必须做些什么来使它成为一个多目标项目。

首先，我将从什么是多目标的定义开始。直到最近，每。NET 项目是基于特定的运行时，如。NET Framework 或 Xamarin iOS，而工件是针对该运行时的。。NET 标准的引入是为了提供一个假的运行时，它实际上运行在一堆不同的运行时上，但这更进了一步。该项目不是一个目标框架，而是许多目标框架，在编译期间，它将为每个目标框架编译输出。所以如果你有。NET Framework *和* Xamarin iOS，您将从一个项目中获得两种输出。

这听起来像是过去的 PCL 时代吗？这似乎是它的灵感来源。早在 PCL 时代，库作者会使用一种叫做“诱饵和开关”的技巧，在这种技巧中，他们编写一个 shell 接口，通过可移植库进行引用，并依赖 NuGet 的实现细节，这将导致它为给定的运行时选择最具体的实现。这样一来，如果一个“便携”和“的话。NET Framework”版本在同一个包中。NET Framework 更喜欢后一种变体。如果出现了一个不受支持的平台，那么它会简单地在该平台上抛出异常，因为这实际上是在“诱饵”程序集中生成的。

这种方法很受欢迎，现在几乎已经成为官方。multi target 项目是一个帮助解决这个问题的工具，因为它可以一次生成许多平台来打包成一个 NuGet 包。因此，让我们来了解一下具体需要做些什么。

第一步:在你的解决方案旁边创建一个`global.json`文件。内容如下。版本应该是可用的最高版本(在撰写本文时是 1.6.68):

```
{
    "msbuild-sdks": {
        "MSBuild.Sdk.Extras": "1.6.68"
    }
} 
```

此步骤使您能够创建一个新的项目类型，该项目类型添加到当前。NET Core SDK。这是必要的，因为。NET Core 不支持，也可能永远不支持 Xamarin、UWP 和其他现成的产品。这个 SDK 是由柳文欢·诺沃特尼写的，他写了许多令人惊奇的东西。所以你可以相信它不会消失。

第二步:准备好之后，您需要手工编辑您的`.csproj`文件来使用这个 SDK，而不是默认的 SDK。只需替换第一行

```
 <Project Sdk="Microsoft.NET.Sdk"> 
```

用

```
<Project Sdk="MSBuild.Sdk.Extras"> 
```

第三步:最后一步，找到你的`<TargetFramework>`条目，改成分号分隔的`<TargetFrameworks>`条目(复数)。例如

```
<TargetFramework>netstandard2.0</TargetFramework> 
```

变成了

```
 <TargetFrameworks>netstandard2.0;netcoreapp2.0;net461;uap10.0.16299;MonoAndroid80;Xamarin.iOS10</TargetFrameworks> 
```

就是这样！现在当你编译的时候，你会发现你的 bin/Debug 或者 bin/Release 文件夹会包含几个条目，每一个条目对应一个你的目标框架。您甚至可以使用特定于平台的代码，但是您需要要么只在为支持这些功能的框架进行编译时包含它(更高级，超出了本文的范围)，要么用`#if`块保护源代码。作为提示，上面的定义将按顺序自动添加到每个目标框架中:

*   `NETSTANDARD2_0`和`NETSTANDARD`
*   `NETCOREAPP2_0`和`NETCOREAPP`
*   `NETFRAMEWORK`和`NET461`
*   `UAP_10_0_16299`
*   以及大量 android API 级别的特定机器人
*   `__IOS__`

快乐穿越平台化！