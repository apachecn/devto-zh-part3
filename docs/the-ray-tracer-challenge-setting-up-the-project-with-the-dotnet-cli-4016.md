# 光线跟踪器挑战-使用 dotnet CLI 设置项目

> 原文：<https://dev.to/simonech/the-ray-tracer-challenge-setting-up-the-project-with-the-dotnet-cli-4016>

它来了，我的旅程的第一个职位开发射线追踪器使用。NET 核心，只使用 VS 代码。如果你没有，我推荐你阅读我的[介绍文章](http://codeclimber.net.nz/archive/2019/05/22/raytracer-challenge-netcore-intro/),它解释了我想要达到的目标和原因。

在第一篇文章中，我将谈论我决定使用的库和工具，以及我如何使用`dotnet` CLI 设置项目结构。

## 使用的工具

我唯一使用的两个工具是 VS 代码和`dotnet` CLI。让我们看看我是如何配置这两者的。

### VS 代码

正如我在本系列文章的[介绍中提到的，我使用的是 VS 代码，当你下载 VS 代码时会有基本的](http://codeclimber.net.nz/archive/2019/05/22/raytracer-challenge-netcore-intro/) [C#/Omnisharp 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。此外，我还使用了另外两个扩展:

*   C#扩展，它增加了一些漂亮的上下文菜单和一些额外的重构；
*   [。NET Core Test Explorer](https://marketplace.visualstudio.com/items?itemName=formulahendry.dotnet-test-explorer) ，它为工作区中的所有测试添加了一个树形视图 UI。

### 点网 CLI

我使用的另一个工具是。NET 核心，就是`dotnet` CLI。我在浏览文档时了解到的一件事是，它支持制表符结束，但是，为了拥有它，必须对它进行配置。您可以按照[页面上的步骤操作如何启用制表符补全。NET 核心 CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/enable-tab-autocomplete) 中。NET 核心文档站点。

但是，简而言之，将下面的代码添加到`~/.bashrc`文件中。

```
# bash parameter completion for the dotnet CLI

_dotnet_bash_complete()
{
  local word=${COMP_WORDS[COMP_CWORD]}

  local completions
  completions="$(dotnet complete --position "${COMP_POINT}" "${COMP_LINE}")"

  COMPREPLY=( $(compgen -W "$completions" -- "$word") )
}

complete -f -F _dotnet_bash_complete dotnet 
```

通常基于 Linux 的系统会在每次终端打开时运行这个脚本。但是默认的 MacOS 终端应用程序运行另一个文件`~/.bash_profile`(通常只在登录时执行)。其他终端可能会有不同的行为，所以最好在两个地方都添加脚本。您可以在另一个文件中复制相同的代码，或者只在`.bash_profile`文件中包含以下行，这样它就会自动调用`.bashrc`。

```
if [-f ~/.bashrc]; then
   source ~/.bashrc
fi 
```

## 使用的库:xUnit

我很确定一些 NuGet 包已经存在，用于计算向量和矩阵计算，以及保存图像。但这将使整个行动失去意义。我引用的唯一外部库是测试框架。

这本书已经以小黄瓜格式提供了完整的测试套件，所以为了节省时间，显而易见的选择是使用。黄瓜网对应: [SpecFlow](https://specflow.org/) 。

但是尽管有支持。NET Core ，似乎 SpecFlow 很大程度上依赖于它的 Visual Studio 插件来解析特性文件和生成可执行测试。我找到了一些解释如何在没有的情况下做到这一点的帖子，但我宁愿开始编码，而不是花时间解决一个特定测试框架的限制。

因此，我将从 xUnit 开始，在前几章完成后，返回，尝试让 SpecFlow 继续工作。NET 核心，并重新实现测试。

## 使用 dotnet CLI 创建项目结构

项目很简单:两个项目(一个。NET 标准类库和一个测试项目)以及将它们链接在一起的解决方案。

这是我在这个过程中学到的第一点:CLI 太棒了！您可以创建解决方案文件、项目，还可以向解决方案中添加项目，以及从一个项目向另一个项目添加引用。

所有这些都不需要对文件进行任何手动编辑。

```
dotnet new sln -o src -n ray-tracer-challenge
cd src
dotnet new classlib -o raytracer -n codeclimber.raytracer
dotnet new xunit -o xUnit -n codeclimber.raytracer.xUnit
dotnet sln add **/*.csproj
cd xUnit/
dotnet add reference ../raytracer/codeclimber.raytracer.csproj 
cd ..
dotnet build
dotnet test 
```

这个命令列表在名为`src`的文件夹中创建了一个解决方案，并在它们自己的子文件夹中创建了两个项目。然后将它们添加到解决方案中，将测试项目中的引用添加到类库中，最后，构建并运行测试。

您可以通过进入该项目的 Github repo 并切换到两个分支来查看此时的代码:

*   第 0.1 章——只是一个使用`dotnet` CLI 创建项目结构并确保一切正常运行的脚本。
*   第 0.2 章 -基本项目(lib 和 xUnit 测试库)，代码简单，确保测试工作正常。

## 结论

既然环境设置正确，在下一篇文章中，我将开始实施本书第一章的练习[《光线追踪挑战——你的第一个 3D 渲染器的测试驱动指南](https://amzn.to/2Elaxkr)。

你可以在系列的[介绍中看到完整的帖子列表。](https://dev.to/archive/2019/05/22/raytracer-challenge-netcore-intro/)