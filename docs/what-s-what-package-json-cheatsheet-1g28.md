# 什么是什么？- Package.json cheatsheet

> 原文：<https://dev.to/areknawo/what-s-what-package-json-cheatsheet-1g28>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

最近我开始了一个关于 Node.js 及其内置 API 的 [**系列。在那里，正如我所说的，我们将深入探索每一个 Node.js API(或者至少这是计划)。但是，谈论 Node.js 而不提及著名的**](https://areknawo.com/nodejs-inside-out-modules-api-rediscovery/) **[**package.json**](https://docs.npmjs.com/files/package.json) 🗄将是对该文件重要性的巨大无知的表现。**

我想我们都知道 package.json 是什么。几乎每个新的 JS 相关项目都是通过设置这个特定的文件开始的。尽管如此，开发人员对如此重要、看似简单的 JSON 文件的了解往往仅限于少数几个领域。当然，这没什么不对——您不必了解整个规范，但至少对什么是什么有一些线索是很好的。这在例如创建**您的第一个公开 NPM 包**时尤为重要。👶而且，由于网络上不一致、不完整或难以阅读的文档，许多新来者经常发布他们的第一个包...与**的错误**。🚫

综上所述，在本文中，我们将最终修复这个缺陷。我们将尽可能深入地探索 package.json！所以，考虑书签🔖此页面供以后用作成熟的 **package.json cheatsheet** ！📋我们开始吧！

# 基础知识

## 名称

我认为`name`属性不需要解释。如果没有这个**必填字段**，您的软件包将无法正常运行。在命名新包时，只有一些规则必须遵守:

*   你的名字应该是**唯一的**(发布到 NPM 时)☝；
*   如果你有适当的 NPM 组织注册，你可以用所谓的**范围**使用你的包，例如@ organization/package；
*   名称的字符长度应不超过 **214 个字符**，包括作用域；
*   **无大写字母**和**下划线** ( **_** )或**点** ( **)。** ) **上开头**；
*   您只能使用 **URL 安全字符** -您的名字很可能会被其他人在终端中键入，并被用作 NPM 页面的 URL；

## 版本

第二个必选且显而易见的字段。`version`，与名称一起组成一个**唯一 ID** ，用于**每次发布**您的包。因为，你猜怎么着！-您应该/必须在每个新发布的软件包中更改版本！同样，你的版本字符串应该可以被 [**node-semver**](https://github.com/npm/node-semver) 解析，这意味着它应该有一个确定的、可预测的**结构**。🏗浏览 NPM 时，您可能已经看到了所有可能的版本代码。通常，它以 3 个数字的形式出现，用点(.)，如 **0.0.0** 。之后，一个可选标签(如**下一个**、 **beta** 、 **alpha** )前面加一个破折号，后面可选加另一个点和数字，如 **0.0.0-next.0** 。当然，你不应该随便丢弃任何你认为合适的新版本(尤其是当你的包被其他人使用的时候)。这就是为什么**版本指南**像 [**语义版本**](https://semver.org/) 被创造出来。

# 信息

## 描述

最好至少给你的用户提供一些关于你的包的信息。ℹ:在你的主 JSON 文件中放一个短的`description`字符串就可以很好地达到这个目的！然后，它将显示在 **NPM 列表**中，例如在 **VS 代码弹出窗口**中。当然，这还不够，所以在项目的根目录下放一个 README.md 文件是个好主意。提到的文件以后可以用于你的包的 **NPM 页面**。

## 关键词

`keywords`给你一个选项，提高你的包的*【SEO】*。😅当你的潜在用户通过你提供的关键词搜索 NPM 列表时，这个字符串数组将使你的包**排名更高**。

## 执照

`license`是一个简单而简短，但却非常重要的领域。正是这个字符串让你的用户知道你在什么条件下共享你的代码。该字符串必须是**(简称)之一，如 **MIT** 、 **GPL-3.0** 等。如果你还不知道哪种许可证最适合你，可以考虑看看 [**这个页面**](https://tldrlegal.com/) ，在这里你可以快速了解并挑选出最好的一个(包括 SPDX 标识符！).稍后将您选择的许可证的实际文本放在项目的根目录下的 **LICENSE.md** 文件中是一个好的实践(如果不是一个要求的话)。📝**

 **## 首页

如果你有一个漂亮的**登陆页面**，你可以自由地把它的**网址**放在这里。它将会在 NPM 网页和其他地方展出。

## 储存库

如果你在 NPM 上公开发布你的软件包，很有可能是某种开源软件。因此，关于实际的**源代码**的位置的信息📦可能会派上用场。`repository`字段可以做到这一点！有两种方法可以设置它——一个具有 2 或 3 个属性的**对象**，即`type`(您的存储库类型，如字符串中的 **"git"** 或**" SVN "**)`url`(您的存储库的 URL)和 repo 中可选的`directory`(如果是例如 **monorepo** )。另一种可能的形式是"**提供者** : **用户** / **回购**"格式的**字符串**(也可用于直接从回购安装软件包)，其中提供者可以是 **"github"** ， **"gitlab"** 或 **"bitbucket"** 或**gist**:**id**

 **## 虫子

是啊，这种事情发生得如此频繁，以至于它们应该拥有自己独立的领域。😅它通常应该指向您的存储库的**问题页面**或任何其他可以**报告这些问题的地方**。

## 作者

这个包的第一个创建者应该得到**适当的奖励**。👌该字段可以有两种可能的值类型:对象或字符串。**对象**可以有 3 个属性- `name`、`email`和`url`(针对作者的网站)。另一种更短的格式是**字符串**版本，带有预定义的格式(与对象相比)——**"*名称* < *电子邮件* > ( *url* )"** 。这些不同的括号是必需的，但并不是所有的括号都需要提供。例如，您可以省略(url ),只提供姓名和电子邮件。NPM 会乐于接受任何变化。

## 贡献者

和作者本人一样重要。👬而且，每个人都应该注意到，这也有一个特殊的属性！`contributors`是一个对象或字符串的**数组，其中应用了与单数作者字段相同的规则。另一个有趣的特性是，您可以在项目的根目录下提供适当的 **AUTHORS.md** 文件，其中将逐行提供项目的贡献者(以前面提到的字符串格式)。这将在以后用作贡献者**默认值**。**

# 文件

## 文件

您很可能希望最终的软件包只包含某些文件。要做到这一点，你有两个选择。您可以在 package.json 中提供一个`files`属性，以字符串的**数组的形式(支持**单独的文件**、**目录**和**通配符**🌟)，以**在发送到 NPM 的包中仅包含某些文件**。另一种选择是提供**。npmignore** 文件(像流行的 [**)。gitignore**](https://git-scm.com/docs/gitignore) )稍后将用于**排除某些文件**。当然，有一些文件永远不会遵守那些规则，并且将永远被包含(例如 **README.md** 或 **LICENSE.md** )或排除(例如 **node_modules** 或**)。git** )。**

## 主

大概大家都知道`main`领域。它应该指向 default，整个包中最重要的文件(**入口点**)。无论`files`配置如何，它都将包含在最终捆绑包中。

## 浏览器

通过`browser`属性，我们可以得到你的包的主文件的不同变体。例如，当您使用某种输出不同格式的模块捆绑器(如**生命**或 **UMD** )时，可以使用它。浏览器字段应该指向文件，该文件可能是在浏览器 🖥中使用的**，并且依赖于该环境的**全局变量**(例如`window`)。**

## unpkg

也许它不是 100%官方的，但[**un pkg**](https://unpkg.com/)as**NPM CDN**已经获得了如此多的人气和用户群，以至于这个属性可能值得仔细看看。`unpkg`应该指向一个文件，该文件以后将由 UNPKG 专门用来提供其 CDN 支持。通常与前面提到的`browser`相同。

## 模块

如果您有一个，`module`属性应该指出这个文件是您的模块化(非捆绑)代码库的入口点。它的目标是更现代的环境。🚝

## 打字

`typings`或`types`(更短的选择)字段显示了 [**打字稿**](https://typescriptlang.org/) 和它提供的**大开发工具**已经变得多么流行。这个属性应该指向你的**类型脚本声明文件**的入口文件(如果你有一个的话)。它稍后将被发送到 NPM，可供下载，并为您的用户提供良好的 ide 支持。至少在我看来，这比将你的打字单独上传到类似于 [**的明确键入的**](https://definitelytyped.org/) 中要方便一些。

## 斌

如果您的包是某种可执行文件，它必须包括这个字段。它应该指向你的 **Node.js 可执行文件**的**主文件**或者有一个**对象**的形式，带有对应于**不同可执行文件**的键和对应于**它们的文件**的值。请记住，您应该以这个神奇的✨ - `#!/usr/bin/env node`行开始您的可执行文件。

## 男人

如果您有任何关于您的软件包的文档，以 [**手册页**](https://en.wikipedia.org/wiki/Man_page) 的形式，请随意在这里提供。它可以是一个指向**单个文件的字符串，也可以是一个这样的数组**。

## 目录

这可能是一个神秘的领域，没有多少人知道它到底是做什么的。🤔我会告诉你-它大多只是**元信息**。正好两个字段提供了一些功能，没有其他功能。当然，它们都应该有一个指向**不同目录**的字符串形式。

*   关于你的库在你的包中确切位置的元信息；
*   `bin` -你所有的**可执行文件**所在的目录。可以用来代替提供不同的文件，一个接一个，用`bin`属性。要知道你不能同时使用这两个属性- **只有其中一个**；
*   `man` -您所有的**手册页**所在的目录。您可以使用它来代替通过`man`属性提供一个文件数组。这当然不那么令人厌倦；
*   `doc` -关于给定包的**降价文档**所在目录的元信息；
*   `example` -你所在目录的元信息**示例代码**；
*   `test` -你的**测试文件**所在目录的元信息；

请记住，正如 [NPM 官方文档](https://docs.npmjs.com/files/package.json#directories)所提到的，这些数据可以在未来用于提供一些额外的功能，例如漂亮的文档或其他功能...

# 任务

## 脚本

我想你很清楚`scripts` field 是做什么的。这是一个简单的对象，键对应于命令，值对应于命令应该做的事情。您最有可能使用像**【构建】**或**【开始】**这样的脚本名称，但是您是否知道有一些脚本会在预定义事件发生时自动执行？它们有很多，你可以在这里找到 [**完整的列表**](https://docs.npmjs.com/misc/scripts) 。👈

## 配置

属性有一个特殊对象的形式，在这里你可以指定一些**配置**，你可以稍后**在你的脚本**中使用它们。👨‍💻例如，一个名为`port`的配置属性可以在以后使用`npm_package_config_port`来引用，其中前面的部分总是必需的。这些选项也可以使用`npm config set [package]:[prop] [value]`进行更改。

# 依赖关系

## 依赖关系

每个人都知道什么是 **NPM 依赖**...关于他们能有多深的迷因。😅而`dependencies`是负责所有这些的字段。在这里，您的包**需要安装(并且最有可能被使用)的所有依赖项必须被列出**。 [`npm install`](https://docs.npmjs.com/cli/install) 或者 [`yarn add`](https://yarnpkg.com/lang/en/docs/cli/add/) 会自动**为你管理这些**。记住你也可以从**URL**、**Git URL**、**GitHub URL**、**链接包**和**本地路径**中列出&安装依赖项。

## 依赖关系

**开发依赖**(那些安装了`--save-dev`或`--dev`的)意味着包含了**开发过程**中需要的所有依赖🏗给定包的。例如可以是**测试框架**、**模块捆绑器**或**传输器**。所有这些都不会被安装用于软件包的标准使用。

## 对等依赖

**对等依赖**(这次**没有自动配置**)允许你指定你的包与其他包的兼容性。这应该有一个**对象**的形式，用**兼容的包名**作为键，用**它们各自的版本**(跟在 node-semver 后面，例如 0.x.x)作为值。从 NPM v3 开始，默认情况下不会安装这些依赖项。

## 可选依赖项

如果你的软件包中有**可选的**，也就是说，并不一定要安装，但是会有所帮助，你可以在这里添加它们。这些将仅在可能的情况下安装(例如，如果平台兼容)。常见的例子是一个名为[**f 事件**](https://www.npmjs.com/package/fsevents) 的 NPM 模块，只在 Mac OS 上可用。

## 捆绑依赖

**捆绑的依赖项**应该有一个数组的形式，其中包含将与您的最终包捆绑在一起的依赖项的名称。这在用 **tarball 文件**保存您的项目时会很有用，使用 [`npm pack`](https://docs.npmjs.com/cli/pack) 捆绑的 tarball 文件将包括这里指定的文件。📦

# 平台

## 引擎

`engines`是一个非常有用的属性(就像其他任何属性一样)，允许你指定一个**库**和**运行时**的对象(就像 **Node.js** 、 **NPM** 或[T10】React Native](https://facebook.github.io/react-native/))。对象键对应于特定的名称(就像 NPM 上的那些——小写，虚线)和兼容**版本字符串**形式的值(node-semver-compatible)。当您的包依赖于**现代特性**(仅在最新的 Node.js 版本中可用)或其他通常全球安装的库和运行时时，这尤其有用。

## os

如果你的包只能在**特定的操作系统**上运行，你可以用`os`属性指定这个事实，以**操作系统代码名**数组的形式。您可以只指定允许的(如`["linux"]`)或前面没有**感叹号** ( **！**)(如`["!win32"]`)。

## cpu

就像使用`os`一样，通过使用`cpu`属性，您可以指定您的代码可以在哪种 CPU 上运行。同样的规则(包括&排除)适用(例如`["x64"]`或`["!arm"]`)。

# 出版

## 私人

如果你想让你的包保持**私有**(或者一个多报告的**条目)，你可以设置`private`属性为**真**。这将确保你的包**不会被发布**，即使你犯了一个错误并试图这样做。默认为**假**。🔒**

## 发布配置

如果你愿意(或者背后有一个真正的原因)，你可以在发布你的包之前用这个特殊的属性覆盖任意的 [**多个 NPM 配置值**](https://docs.npmjs.com/misc/config#config-settings) 。这应该有一个对象的形式。最有可能的是，你只想改变像`"tag"`、`"registry"`或`"access"`这样的值。

# 自定义字段

除了上面所有的字段，package.json 已经成为各种不同工具放置自己的、**自定义字段**和**配置**的地方。就像前面提到的 **UNPKG** ， [**巴别塔**](https://babeljs.io/docs/en/config-files) ， [**漂亮点**](https://prettier.io/docs/en/configuration.html) 💅大量不同的工具允许你这样做。几乎总是写在他们的**文档**中。但是，有了这么多的工具，package.json 变得有点...**拥挤**。👎因此，依我看，最好是尽可能使用**单独的配置文件**,只为上面列出的所有内容保留 package.json。

# 就这样！

我真的真的希望这篇文章能帮助你们中的一些人发布**你们的第一个公共包**或者让你们**学到一些新的东西**。🌟这是我希望几周前就拥有的知识。😅通过这篇文章，甚至作为一个作者，我也提高了对所有 package.json 内容的理解。

一如既往，在评论里写下你对这篇文章的看法****在下面留下你的反应**！它极大地激励了我！此外，考虑一下**在 Twitter**[**上关注我的**](https://www.facebook.com/areknawoblog) [**，在我的 Facebook 页面**](https://twitter.com/areknawo) 上关注我的 [**个人博客**](https://areknawo.com) 以使**及时了解** 🗓的最新内容。当然，感谢您的阅读，我将在下一篇文章中与您见面！******