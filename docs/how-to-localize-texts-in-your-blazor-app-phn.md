# 如何在您的客户端 Blazor WebAssembly 应用程序中本地化文本？

> 原文：<https://dev.to/j_sakamoto/how-to-localize-texts-in-your-blazor-app-phn>

# 简介:什么是“Blazor”(客户端)？

“Blazor”(客户端)是一个单页面 Web 应用程序(也称为“SPA”)框架。

客户端 Blazor 允许你运行 C#代码和。NET Standard 2.0 本机程序集(。dll)在现代 web 浏览器的 WebAssembly 引擎之上，没有任何。NET 服务器进程。

如果你是 Blazor 的新手，我推荐你看这个 YouTube 视频。

[NDC 奥斯陆 2017 -“网络应用不能真的做*那个*，对吧？——史蒂夫·桑德森”](https://www.youtube.com/watch?v=MiLAE6HMr10&feature=youtu.be&t=31m59s)

视频讲的是 Blazor 的原型，我写这篇帖子的时候目前的版本是 v.0.9.0，有一些突破性的变化。但那个视频对理解“Blazor”的概念和利弊是有用的。

# 客户端 Blazor WebAssembly App 本地化

## 本条范围

本文将只讨论文本本地化场景。

日期、时间和货币格式超出了本文的范围。

另外，服务器端 Blazor 也不在本文讨论范围内。
*【2019/11/09】[【Blazor I18n 正文】库 ver.7](https://www.nuget.org/packages/Toolbelt.Blazor.I18nText/) 开始支持服务器端 Blazor！🎉*

## 文字本地化已经有解决方案了吗？

是的，我们可以在网上找到很好的解决方案和相关文章。

例如:

*   **“使用微软的 Blazor 应用程序的本地化。JSInterop"**
    *   [https://www . c-sharp corner . com/article/localization-in-blazor-app-using-Microsoft-jsinterop/](https://www.c-sharpcorner.com/article/localization-in-blazor-app-using-microsoft-jsinterop/)
*   **“将 ASPNET 核心作为后端服务的 Blazor 应用程序国际化”**
    *   [https://remibou.github.io/I18n-with-Blazor-and-ASPNET-Core/](https://remibou.github.io/I18n-with-Blazor-and-ASPNET-Core/)

然而，我对上述解决方案并不满意，因为这些解决方案有以下考虑点。

# 我对上述解决方案不满意的 4 个原因

## 1。它需要服务器端的实现。

Blazor 是一个客户端解决方案，因此，Blazor 应用程序可以部署在静态内容 HTTP 服务器上，如 GitHub 页面。

然而，上述本地化解决方案需要一个服务器端的 ASP.NET 核心实现，当然它需要可以运行 ASP.NET 核心的 Web 服务器。

## 2。它需要。resx 编辑器。

。resx 文件只是一个 XML 格式的文件，在一般 C#编程中的本地化场景中很流行使用。

但是，要编辑。resx 文件，您将需要 Visual Studio。编辑工作很辛苦。我想是带有文本编辑器的 resx 文件。

## 3。它需要通过“字符串”访问密钥。

常见的文本本地化解决方案是从“键”获取本地化文本。

在上面的解决方案中，你必须在 C#/Razor 源代码中逐串描述 key。

将一个键写成字符串经常会导致输入错误，而这种错误在编译时是检测不到的。

它也无法获得编辑器的代码补全功能的帮助。(例如。“智能感知”)

我真的想得到静态类型解决方案，而不是后期绑定解决方案，比如通过字符串访问键

## 4。它不考虑 Blazor 组件库。

Blazor 支持创建和重用组件库作为 NuGet 包。

然而，上述解决方案无法解析 Blazor 组件库包中的本地化文本。

# 我决定做一个新的方案。

如上所述，我不能满意我在互联网上找到的任何解决方案，因此，我决定制定一个新的解决方案。

## 我的做法是...

我的方法中最重要的一点是，不仅要编写库代码，还要在构建时编写用于处理某些任务(如生成静态类型文件、生成本地化的文本文件作为静态内容)的构建脚本。

*   使用简单的 JSON/CSV 格式，而不是。resx 文件格式来编写键本地化文本对应表(我称之为“本地化文本源文件”)。
*   在构建时自动从“本地化的文本源文件”生成静态类型的 C#类(我称之为“文本表类”)源代码文件。
    *   “文本表类”具有与“本地化测试源文件”中的每个关键字相关联的字段。
*   并且还会在自动构建时从“本地化文本源文件”中的“wwwroot”内容文件夹下生成 JSON 格式文件(我称之为“本地化文本资源 JSON 文件”)。
    *   “本地化文本资源 JSON 文件”作为静态内容文件部署在 Web 服务器上，这些文件在运行时从 Blazor 客户端进程中获取。
*   包括一个“msbuild”脚本，使“本地化的文本资源 JSON 文件”包含在 Blazor 组件库的 NuGet 包中。

# ...而且，我做到了！

我的工作成果在这里:

*   **Blazor 国际化(I18n)文本**--" tool belt。Blazor.I18nText" NuGet 包
    *   [https://www.nuget.org/packages/Toolbelt.Blazor.I18nText/](https://www.nuget.org/packages/Toolbelt.Blazor.I18nText/)

## 没有。resx，简单的 JSON 格式，也可以是 CSV 格式！

[![fig](img/5a5f74f77c0555a9004799e7b15d0d59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vGn27IgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.001.png)

## 在 GitHub 页面上有效。(仅静态内容 HTTP 服务器)

*   [https://jsakimoto . github . io/tool belt。blazor . i18 context/](https://jsakamoto.github.io/Toolbelt.Blazor.I18nText/)

[![movie.1](img/7366e12a04d288ac128d76613f0bdc5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--StI6iDzu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/movie-001.gif)

## 智能感知效果不错。

[![movie.2](img/78f2c70528adc6a103e76cdcdef4c95f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FHUFkIwZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/movie-002.gif)

# 怎么用？

### Step.1 -添加“工具带。Blazor.I18nText "包

将 NuGet 包添加到您的 Blazor 应用程序项目中。

如果您使用的是 dotnet CLI，您可以通过下面的命令行来完成。

```
$ dotnet add package Toolbelt.Blazor.I18nText 
```

如果您在 Windows 操作系统中使用 Visual Studio，也可以在 Visual Studio 的包管理器控制台中执行此操作。

```
PM> Install-Package Toolbelt.Blazor.I18nText 
```

### 步骤 2 -以 JSON 或 CSV 格式创建本地化文本源文件

在 Blazor app 项目文件夹下的`i18ntext`文件夹中添加每种语言的本地化文本源文件。

本地化的文本源文件必须是简单的只有键值的 JSON 文件，如下例所示，

```
{  "Key1":  "Localized text 1",  "Key2":  "Localized text 2",  ...  } 
```

或者，只有 2 列的 CSV 文件，没有标题行，如下例所示。

```
Key1,Localized text 1
Key2,Localized text 2 
```

**注意**-CSV 文件的编码必须是 **UTF-8** 。

并且，本地化文本源文件的命名规则必须如下。

```
<Text Table Name>.<Language Code>.{json|csv} 
```

[![fig.1](img/5a5f74f77c0555a9004799e7b15d0d59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vGn27IgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.001.png)

### 步骤 3 -总是在创建或更新本地化文本源文件时构建项目。

在创建或更新了那些本地化的文本源文件之后，你必须构建你的 Blazor 应用程序项目。

项目构建完成后，**“类型化文本表格类”C#文件**将通过构建过程在`i18ntext/@types`文件夹中生成。

另外，**“本地化文本资源 JSON”文件**也会在`wwwroot/content/i18ntext`文件夹下生成。

[![fig.2](img/c5cc835c7e7781745d1aaa2ae248dc2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8rizQ06B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.002.png)

**注意**——如果你想每当那些本地化的文本源文件(.json 或者。csv)被更改，您可以使用带有以下参数的`dotnet watch`命令。

```
$ dotnet watch msbuild -t:CompileI18nText 
```

输入此 dotnet CLI 命令后，dotnet CLI 进程会继续运行，并观察本地化文本源文件的变化。当持续运行的 dotnet CLI 检测到本地化文本源文件发生变化时，dotnet CLI 将本地化文本源文件重新编译成**“类型化文本表类”**文件和**“本地化文本资源 JSON”文件**。

[![fig.2-2](img/40b509d37cc28093e7c6ee307dcf2217.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nmS7XjQe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.002-2.png)

### 步骤 4 -配置您的应用程序以使用 I18nText 服务

在编辑器中打开 Blazor app 的“startup”类的 C#源文件，添加用于打开`Toolbelt.Blazor.Extensions.DependencyInjection`命名空间的`using`子句，并在 Startup 类的`ConfigureServices()`方法中添加以下代码。

```
services.AddI18nText<Startup>(); 
```

[![fig.3](img/59b68a075c0d9f3a6d64bd5fc4ee632e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VMsMDouB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.003.png)

### Step.5 -获取 Blazor 组件中的“文本表格”对象

打开您的 Blazor 组件文件(。cshtml ),并执行以下操作:

1.  将`Toolbelt.Blazor.I18nText.I18nText`服务注入组件。

```
@inject Toolbelt.Blazor.I18nText.I18nText I18nText 
```

1.  添加从本地化文本源文件生成的文本表类的字段，并分配默认实例。

```
@functions {

    I18nText.MyText MyText = new I18nText.MyText(); 
```

**注意**——文本表格类的命名空间是`<default namespace of your Blazor project>` + `"I18nText"`。

1.  覆盖 Blazor 组件的`OnInitAsync()`方法，将一个文本表对象(即`I18nText`服务实例的`GetTextTableAsync<T>()`方法的返回值)赋给文本表字段。

```
protected override async Task OnInitAsync()
{
  MyText = await I18nText.GetTextTableAsync<I18nText.MyText>(this); 
```

[![fig.4](img/033b3df8bee7ec1104de3144bba38a1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6txeot13--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.004.png)

### Step.6 -使用文本表格

完成这些步骤后，您可以引用 Text Table 对象的字段来获取本地化的文本。

如果您在 Windows 操作系统中使用 Visual Studio，并且在该 Visual Studio 中安装了 Blazor extensions，则可以获得“智能感知”和“文档注释”支持。

[![movie.2](img/78f2c70528adc6a103e76cdcdef4c95f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FHUFkIwZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/movie-002.gif)

***注意:*** Text Table 对象允许你动态地通过关键字串得到本地化的文本，用索引器语法，就像这样。

```
<h1>@MyText["HelloWorld"]</h1> 
```

这种方式有时被称为“后期绑定”。

这个特性在某些情况下非常有用。

但是，如果你犯了一些键串的错别字，这些错误在编译时不会被发现。
在这种情况下，它将返回密钥字符串，没有任何运行时异常。

### Step.7 -运行它！

构建并运行你的 Blazor 应用程序。

I18nText 服务检测 Web 浏览器的语言设置，并读取最适合所检测语言的本地化文本资源 JSON。

[![fig.5](img/f86ed77dd617d25a0e0874d0a3b232cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sl9vAhPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jsakamoto/Toolbelt.Blazor.I18nText/master/.assets/fig.005.png)

# 了解更多

你可以在 GitHub 上找到更多关于我的工作的细节。

*   [https://github . com/jsakmoto/tool belt。blazor . i18 上下文](https://github.com/jsakamoto/Toolbelt.Blazor.I18nText)

* * *

用 Blazor 快乐编码:)