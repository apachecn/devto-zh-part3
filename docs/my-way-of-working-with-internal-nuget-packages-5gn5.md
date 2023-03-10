# 我使用内部 nuget 包的方式

> 原文：<https://dev.to/engberrg/my-way-of-working-with-internal-nuget-packages-5gn5>

[![](img/877d45b7c8befc68ef4e3f094b183949.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--82Fv6vsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AA4sh2W_Gt5OWFg3HHzwXPw.png) 

<figcaption>图片借用自[https://cdn . pix abay . com/photo/2013/07/13/10/22/box-157074 _ 1280 . png](https://cdn.pixabay.com/photo/2013/07/13/10/22/box-157074_1280.png)</figcaption>

我工作的公司足够大，可以跨越多个开发团队。为了能够共享一种共同的工作方式，并防止轮子被反复发明，我们依赖于共享的 [nuget](https://docs.microsoft.com/en-us/nuget/what-is-nuget) 包。

我是许多软件包的作者，也是这些软件包的主要贡献者，这需要我花一些时间来维护它们。为了能够做到这一点，我需要一种能让我高效完成这项工作的方法。

在这篇文章中，我将一步一步地分享我的方法。

#### 版本控制

使用 git 对包[进行版本控制。Git 是一个*事实上的标准*，无需进一步描述:)](https://git-scm.com/)

#### 命名

决定一个适合你的好的命名约定。我们使用公司前缀，用点分隔，范围缩小。比如:CompanyA。AspNetCore.Mvc 将是我们自己的包，为 ASP.NET 核心 Mvc 定制类。

#### 项目结构

为了保持一致，应该使用项目结构。我的观点是基于大卫·福勒的这个要点。它并没有被严格遵循，而是受到了启发。例如，测试项目没有被分离到它自己的测试文件夹中，而是包含在 src 文件夹中。这个设置只做一次，当我引入额外的包时，我只是从另一个项目中复制结构。这个结构可以很容易地提取到一个[点网模板](https://docs.microsoft.com/en-us/dotnet/core/tools/custom-templates)中。

**Nuspec 文件**

通过添加您的 nuspec 文件，遵循[这个](https://docs.microsoft.com/en-us/nuget/reference/nuspec)引用。

**测试**

确保用单元测试覆盖您的包。这将给你信心，使你的代码像你期望的那样工作，也将帮助其他贡献者。我正在使用 [xUnit](https://xunit.github.io/) 并且在一些项目中使用[autofix](https://github.com/AutoFixture/AutoFixture)来最大化可维护性并且在安排阶段减少代码。

**文档**

向项目中添加一个 README.md，解释如何使用该包。更多关于 README.md 下面关于*制作包*的部分。我正在考虑调查[阅读文件](https://readthedocs.org/)作为补充。

#### 如何引入变化？

使用的工作流是 [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/) ，这意味着我们不直接将变更推送到主分支，而是推送到我们针对开发分支集成的特性分支。我不打算在这里详细介绍 Git 流。

当一个特性完成时，一个拉取请求被打开，利益相关者被邀请。希望这个特性能被合并回去开发，并为下一个即将到来的版本做好准备。git 标签应该反映将要发布的版本。我在制作包的章节中解释了我这样做的过程。

**是否突破性变化？**

那么，什么是突破性的改变呢？

> 一个[软件](https://en.wiktionary.org/wiki/software)系统的一个部分的变化可能导致其他组件[失败](https://en.wiktionary.org/wiki/fail)；最常出现在多个应用程序使用的共享代码库中— [维基百科](https://en.wiktionary.org/wiki/breaking_change)

如果更改向使用者公开的现有方法的定义，或者从对象中移除属性，这是一个重大更改。请确保您有一个针对您的包的消费测试客户端，以便您可以验证您所做的更改是否正确。

当需要更新一个已经存在的包时，你需要考虑你的消费者。你需要问问自己，你引入的是否是一个突破性的改变。如果不是，那也没关系。该更改应该只改变次要版本和/或补丁版本。

如果你要引入一个突破性的改变，在删除它之前，你需要保持对不推荐的特性的支持至少一个版本。我倾向于这样做

1.  添加新功能
2.  通过添加一个 [ObsoleteAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.obsoleteattribute.message?view=netcore-2.1) 来废弃旧特性，并描述消费者应该如何迁移。
3.  删除次要版本并发布新版本

在下一个主要版本中，删除过时的功能。通过这种方式，给消费者一些时间来迁移他们的现有代码以使用新版本。

#### 精心制作包裹

在发布新的包之前，我要确保

1.  nuspec 文件中的依赖项已经更新，并且支持的目标框架已经就位
2.  README.md 更新了新的功能和示例
3.  版本控制正确

**nu spec 中的依赖关系**

nuspec 文件清单中有一个部分能够指定包拥有的[依赖项](https://docs.microsoft.com/en-us/nuget/reference/nuspec#dependencies)。即使包只支持一个目标框架，它们也包含在一个[依赖组](https://docs.microsoft.com/en-us/nuget/reference/nuspec#dependency-groups)中。这使得将来添加额外的目标框架变得容易。

**更新 README.md**

为了确保我更新了 README.md 以便于理解，我在编辑时使用了 [Markdown Live Preview](https://markdownlivepreview.com/) 。这给了我一个可视化的编辑器来显示文件。然后，我将内容复制到我的 README.md 并提交它。

**对包进行版本控制**

我用的是[语义版本](https://semver.org/)。要对包进行版本控制，编号必须在*中保持一致。csproj，。nuspec 和 git 存储库中的。为此，我使用了定制的 PowerShell 脚本。