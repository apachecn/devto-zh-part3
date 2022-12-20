# Blazor 工作原理第 1 部分

> 原文：<https://dev.to/rembou1/blazor-how-it-works-part-1-102>

# Blazor 是如何工作的？第 1 部分:从。剃刀

我一年前开始写这个博客，我很自豪地说，我发表了 22 篇博文，每 2.5 周一篇，这有点低于我每 2 周一篇的第一个目标，但没关系。我现在每个月的访客不到 3000 人，我想这不算多，但足以让我坚持下去。

*这篇博客文章基于 net core SDK 3 . 0 . 100-preview 5-011568，一些内容可能会在未来发生变化。*

我正在开始一个关于 Blazor 内部的系列文章。我将试着找出 Blazor 是如何工作的，一个项目是如何构建的，它是如何在浏览器上加载的，UI 是如何更新的…虽然我将重点放在 Blazor 客户端，但其中一些也可以应用到 Blazor 服务器端(大部分构建过程和渲染逻辑)。在这个系列中，我将边写文章边学习，试图弄清楚代码是如何工作的。我将尝试添加来自微软仓库的参考和代码样本。如果你认为我的理解是错误的，请添加评论，我会修复帖子。

在这第一篇文章中，我将试图弄清楚在 Blazor 项目中输入“dotnet build”后会发生什么，以及 C#类是如何从 razor 文件中生成的。

## 点网 cli

dotnet cli 源代码位于[这里](https://github.com/dotnet/cli/blob/master/src/dotnet/Program.cs)。当我们输入“dotnet build”时，dotnet 程序在它的[内部命令目录](https://github.com/dotnet/cli/blob/master/src/dotnet/BuiltInCommandsCatalog.cs)中寻找一个“build”(这使得开发者能够将其他命令添加到目录中)。

[BuildCommand](https://github.com/dotnet/cli/tree/master/src/dotnet/commands/dotnet-build) 继承自 [MSBuildForwardingApp](https://github.com/dotnet/cli/blob/master/src/dotnet/commands/dotnet-msbuild/MSBuildForwardingApp.cs) ，后者使用[MSBuildForwardingApp withoutloging](https://github.com/dotnet/cli/blob/95c6eff6daa1a69f29c42b2d405400ad44bdec91/src/Microsoft.DotNet.Cli.Utils/MSBuildForwardingAppWithoutLogging.cs)。然后，该类将启动一个命令行，用好的参数执行 MsBuild。正如我用 [procexp](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer) 发现的，命令看起来像这样

```
"dotnet.exe" exec "C:\Program Files\dotnet\sdk\3.0.100-preview5-011568\MSBuild.dll" -maxcpucount -verbosity:m -restore -consoleloggerparameters:Summary -target:Build "-distributedlogger:Microsoft.DotNet.Tools.MSBuild.MSBuildLogger,C:\Program Files\dotnet\sdk\3.0.100-preview5-011568\dotnet.dll*Microsoft.DotNet.Tools.MSBuild.MSBuildForwardingLogger,C:\Program Files\dotnet\sdk\3.0.100-preview5-011568\dotnet.dll" 
```

我们可以看到它没有直接使用 MsBUild.exe，而是使用了 dotnet cli 的子命令“exec”。我找不到这个命令是在哪里定义的，我猜它不是托管代码(正如 Matt Warren 所说[这里](https://mattwarren.org/2016/07/04/How-the-dotnet-CLI-tooling-runs-your-code/))而是负责启动一个新的托管可执行文件的组件(。exe 都死了只有。dll 现在在 dotnet 世界中)，我认为它几乎和“dotnet MsBuild.dll”一样。

## MSBuild

MsBuild.dll 密码位于[这里](https://github.com/Microsoft/msbuild.git)据我了解，切入点是[这里](https://github.com/microsoft/msbuild/blob/4f3c6ed7fbb44681413e39c1aa7044ac82bef166/src/MSBuild/XMake.cs)。我不会深入 MSBuild 的内部细节，因为源代码非常大。基本上，MSBuild 是由许多描述构建过程不同步骤的 XML 文件驱动的。为了定义这个构建过程，我们看到“dotnet exec”向 MSBuild 发送了一个东西:“target:Build”。MSBuild 中的目标就像一个函数或入口点:您执行一个目标，MSBuild 读取连接到该目标的任务和从属目标并运行它。目标定义在一个。targets”文件位于 Sdk 文件夹中，将使用的目标定义由 csproj 文件中的“SDK”属性定义。在 Blazor 应用程序中，它是“微软。Net.Sdk.Web”，所以目标定义会在“C:\ Program Files \ dot net \ SDK \ 3 . 0 . 100-preview 5-011568 \ Sdks \ Microsoft。NET.Sdk.Web\Sdk\Sdk.targets "这个文件只包含其他 Sdk 的目标文件。为了理解启动的不同任务，更详细地运行构建会更容易:

```
dotnet build -v detailed > build.log 
```

build.log 文件将包含这个项目构建中涉及的目标(函数)层次结构，我们必须在 Sdk 文件夹中搜索目标名称以了解它的作用。关于“组件”或“Blazor”的目标是:

*   ResolveRazorComponentInputs:此任务查找所有的"。剃刀”文件，并把它们放入一个变量中
*   _ CheckForIncorrectComponentsConfiguration:检查是否至少有一个 blazor 文件并且 RazorCompileOnBuild 为 true。我认为这意味着在 Blazor 中模板是在构建时构建的，而在经典 ASPNET 核心中你可以在运行时构建 Razor 模板。
*   AssignRazorComponentTargetPaths:对于每个 razor 文件，它为生成的类创建一个路径(. g.cs 文件)
*   _ HashRazorComponentInputs:for each。razor 它计算一个散列并保存在一个. cache 文件中，我猜这一定是为了调试的目的(为了检查构建输出和源代码之间的匹配)。
*   _ ResolveRazorComponentOutputs:我不明白，这是某种检查。
*   RazorGenerateComponentDeclaration:这是. g.cs 类生成的地方
*   我们将在以后博客文章中探讨的其他目标

## RazorGenerateComponentDeclaration

这个目标的主要目的是调用“RazorGenerate”。这个任务在这里被定义为。这个任务本身并没有做很多事情，只是为一个名为“rzc.dll”的 dll 生成一个“dotnet exec”命令行(它必须代表“razor 编译器”)。用 [dotPeek](https://www.jetbrains.com/decompiler/) 反编译这个 dll 后，发现这个 dll 的源代码是[这里](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Tools/)。在跟踪程序入口点之后，我找到了[应用程序类](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Tools/Application.cs)，它有点像我们之前看到的 dotnet cli，它定义了一系列将要执行的命令。RazorGenerate 调用的命令是…等等…是“生成”命令(RazorGenerate.cs 的第 51 行)。

这个 [GenerateCommand](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Tools/GenerateCommand.cs) 创建一个 RazorProjectEngine，以并行方式 4×4(第 304 行)浏览所有文件，并调用 RazorProjectEngine。处理方法在上面。

## RazorProjectEngine。过程

[RazorProjectEngine](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Language/RazorProjectEngine.cs) 是负责初始化代码生成过程的类，该过程由 IRazorFeature 列表组成，是从模板文件生成 C#代码的必要步骤。一个特性可以链接到一个指令(关键字)，所以它可以从模板文件中获取内容，就像“@functions”指令定义的[这里的](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Language/Extensions/FunctionsDirective.cs)那样“简单地”将“@function{}”之间的所有内容添加到类定义中。其中一些步骤是为了”。cshtml“解析，有些是为了”。razor”，当一个指令被注册时，它必须传递它所应用的文件的种类(例如“函数”指令对两者都适用)，文件种类的选择是在 [FileKinds](https://github.com/aspnet/AspNetCore-Tooling/blob/master/src/Razor/src/Microsoft.AspNetCore.Razor.Language/FileKinds.cs) 类上完成的。这些步骤中的一些:

*   ComponentInjectDirectivePass:添加一个属性“Microsoft。AspNetCore . components . inject attribute ",用于与“inject”指令的每个匹配项。
*   ComponentDocumentClassifierPass:将通过 ComponentCodeTarget 或其基类 DocumentClassifierPassBase 之类的子类生成大部分. g.cs 文件(类名、名称空间和 BuildRenderTree 方法)。

你可以在这里全部查看:([https://github . com/aspnet/AspNetCore-Tooling/tree/master/src/Razor/src/Microsoft。AspNetCore.Razor.Language/Components](https://github.com/aspnet/AspNetCore-Tooling/tree/master/src/Razor/src/Microsoft.AspNetCore.Razor.Language/Components)。

## 结论

写这篇文章我学到了很多东西，其中有两件重要的事情:

*   因为语言处理逻辑位于 SDK 中，所以我理解为什么我需要在每次 Blazor 更新之前更新我的 SDK。
*   Blazor/Component 被深深地集成到 Razor 代码和 dotnet SDK 中，所以我们可以说这个项目对他们来说非常重要。如果它不重要，他们会尽量把它放在角落里，这样以后就可以安全地移走了。

这种博客帖子对我来说是新的，因为它主要是阅读并试图理解比我聪明的人写的代码，如果你认为我错过了什么，有些东西需要更多的澄清，或者说错了什么，请发送评论，我会改进我的帖子。

在第一部分中，我们看到了“dotnet build”如何从所有。剃刀锉。在下一部分，我们将看到构建 blazor 项目所需的其他步骤(blazor boot json 文件、链接等)。