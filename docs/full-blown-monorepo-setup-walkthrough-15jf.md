# 全面的 monorepo 设置演练

> 原文：<https://dev.to/areknawo/full-blown-monorepo-setup-walkthrough-15jf>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

我是那种真正喜欢总是使用最新最棒的东西的人。🌟它是好是坏完全是另一回事。但正是这种内心的渴望让我**拓宽了自己的知识面**和阅历。而这一次，它让我到了 **monorepos** ...

**单回购作为一个概念本身并不新鲜。事实上，它已经很老了。但是，随着我们的**代码变得越来越大，我们想要一个越来越好的**结构📈，它开始再次获得巨大的牵引力。因此，在这篇文章中，我们将探讨 monorepos 是什么，它们的主要优势是什么以及其他细节。然后，我们将基于令人敬畏的工具，如 [**Lerna**](https://lerna.js.org/) 、[**TypeScript**](https://typescriptlang.org/)* *和 [**Rollup**](https://rollupjs.org/) ，来配置我们自己的 monorepo 设置！我们开始吧！**

 **# 所以，你说 monorepo？

我猜你已经知道了，或者至少猜到了 monorepo 是什么意思，是怎么回事。Monorepo 是一个术语，指的是你在一个**单一库**中**组织你的代码库**的方式(不是任何种类的技术定义😅).如果你从未读过任何关于 monorepos 为何如此伟大的[几十篇](https://ai.google/research/pubs/pub45424)T6】或 T8】文章，你可能会认为，这样一来，你不断增长的代码库会很快变得一团糟。而且，你猜对了！-你完全错了。

为了更好地理解这一点，让我们把兴趣范围降低到更具体的领域——JS 开发。目前，JavaScript 工作流已经被 **NPM 包**所主导。这种形式允许我们轻松地创建、共享和重用代码。不考虑恶意代码和占用大量空间的大型依赖树的可能性，它们太棒了！🙃表单开发观点，通常是单个包=单个代码库-逻辑。但是如果你开发了一个**软件包生态系统**，它们很可能相互依赖，那会怎么样呢？你甚至可以用一个 [**NPM 示波器**](https://docs.npmjs.com/about-scopes) 让你的包看起来像这样。你会把你的代码放在不同的仓库里吗？你知道一个生态系统被分离并不好，不是吗？此外，事实上**分开发布**，拉请求和整个**管理过程**将会是一个**噩梦**，因为你的包的数量会继续增长。正如你所料，这个问题的解决方案来自于 monorepo。

Monorepos 结合了两个世界最好的东西——单一、**小且易于管理的**存储库与**多功能性和容量**多。👌它只不过是一个具有良好结构的回购协议——每个独立的包都有自己的位置，代码就像平常一样组织在那里。

# 那么，值得吗？

明确地...在**特定情况下**。自然，你不应该从服务于完全不同目的的包中创建一个 monorepo。但是，如前所述，这对于创建一起工作或具有相同目标的包的生态系统是很好的。经验之谈——*只对应该分组的东西进行分组*。所以，下次你真的想一次用不同的代码创建多个不同的包时，考虑一下使用 monorepo 是不是更好。为了不使你陷入完全的黑暗，作为一个**不错的案例研究**，你可以查看流行的 JS 库和工具的源代码及其结构，例如 [**Babel**](https://github.com/babel/babel) 、 [**Jest**](https://github.com/facebook/jest) **、fan-favorite[**React**](https://github.com/facebook/react)、 [**Vue**](https://github.com/vuejs/vue) 和 [**Angular**](https://github.com/angular/angular) 以及

所以，概括一下所有的信息...Monorepo 将**个相似的包**分组，具有**实体结构**和包之间的依赖关系。现在，像**单一发布板**，更容易的**交叉封装变化**和一个**配置**，测试和例子的单一位置是显而易见的。但是，如果没有合适的工具，管理多个独立的、位于不同路径的包并不容易。在 web 开发世界中，这样的功能是由 [**Lerna**](https://github.com/lerna/lerna) 提供的。这个工具，作为一个标准包管理器(像[**【NPM】**](https://www.npmjs.com/package/npm)或 [**纱**](https://yarnpkg.com/) )的高级包装器，是专门为 monorepos 设计的。它让你可以访问大量不同的配置选项和专门的命令，例如在每个包中执行给定的脚本，在单独的包中安装依赖项，管理版本和发布到 NPM。毫不夸张地说，轻松管理 monorepo 所需的所有东西。✨

但是，有这么多不同的选项和命令，很容易很快迷失在这个单一的丛林。这就是为什么我认为是时候我们自己创造一个好的单一回购结构了...

# 如何做单回购？

在这里，我将指导您完成我自己的个人 monorepo 设置。正如之前所说，我们将使用**纱**、**勒纳**、**打字稿**和**卷**。但是，由于我们希望主要关注 monorepo 设置本身，因此我们不会从头开始配置 Rollup 等工具。相反，我将使用我最喜欢的，我在我的项目中使用的**基于卷起的捆绑器**，它需要更少的配置- [**毕丽**](https://bili.egoist.sh/) 。当然，这仅仅是**生产就绪**的汇总。自然，如果你以前使用过 Rollup，你将很容易用它代替毕丽。

## 基础知识

让我们从创建核心包并安装必要的依赖项开始。此时，我希望你已经安装了 Node.js 和 Yarn(或者 NPM)。

```
yarn init 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的命令，您将被引导完成 **package.json** 文件的基本设置。你可以提供你想要的数据，但是一个重要的要求是将`private`选项设置为`true`。这将确保给定的包**永远不会被发布**，在我们的情况下，这意味着它只是我们 monorepo 树的主干。最后，您的 package.json 应该类似于下面这样:

```
{  "name":  "package",  "version":  "0.0.0",  "description":  "Simple package",  "main":  "src/index.ts",  "license":  "MIT",  "private":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将安装所有需要的依赖项，这样我们以后就不必为它们费心了。

```
yarn add lerna typescript bili rollup-plugin-typescript2 --dev 
```

Enter fullscreen mode Exit fullscreen mode

现在，我再来说说 Lerna。从本质上来说，Lerna 是一家单一回购公司。对于大多数你想用的 monorepos 的东西，它都有。所有这些功能都有精美的 [**和精心编写的**](https://github.com/lerna/lerna/blob/master/README.md) 文档。因此，在本教程中涵盖所有这些是没有意义的。相反，我们将只关注那些帮助我们在 monorepo 中设置和工作的命令。简单说一下。😉

我们将从在我们的**根目录**中创建我们的 **lerna.json** 文件开始。这只是一个供 Lerna 读取的配置文件。最好也是最简单的方法就是使用 [`lerna init`](https://github.com/lerna/lerna/tree/master/commands/init#readme) 命令。

```
yarn run lerna init 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将做 3 件事——创建你的**配置文件**,创建**包文件夹**,并将 Lerna 添加到你的`devDependencies`(如果还没有，例如当使用 Lerna 全局安装时)。看看默认的配置文件:

```
{  "packages":  [  "packages/*"  ],  "version":  "0.0.0"  } 
```

Enter fullscreen mode Exit fullscreen mode

字段是我们的包所在的目录或**通配符**的**数组。我个人认为 **packages/** 中的默认位置是最好的，因为它不言自明，不需要我们用单独的包文件夹填充根目录。`version`表示 monorepo 的当前版本——不一定与 package.json 中的版本同步，但最好保持同步。还有一些可用的属性，你可以在这里看到完整的列表，但我想把重点放在只有一个- `npmClient`。在我们的例子中，我们需要将其设置为`"yarn"`。🧶** 

```
{  "npmClient":  "yarn",  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

这自然会表明使用 Yarn 而不是默认的 NPM 来管理我们的包。现在，使用纱线时，你有了一个更重要的选择。这个布尔值会让 Lerna 知道你想让它使用 [**纱线工作空间**](https://yarnpkg.com/en/docs/workspaces) 特性来管理包。Yarn 工作区基本上是管理 monorepos 的一个低级解决方案。它们的工作方式与 Lerna 稍有不同，并且不提供相同的功能集。但是，当与 Lerna 一起使用时，它们可以在**链接依赖关系**时提供**更好的性能**。那么，为什么我们不用它们呢？简单的回答是- **他们没有很好的范围包支持**，对我个人来说，这是一个交易破坏者。Monorepos 和 scope 包配合得非常好，我认为它们的支持是强制性的。

## 套餐

在所有上述工作完成后，我们差不多已经为 Lerna 做好了准备。很简单，你不觉得吗？那么是时候设置一些包了！在这里，你有两个选择——使用 [`lerna create`](https://github.com/lerna/lerna/tree/master/commands/create#readme) 让 Lerna 引导你完成创建新包所需的步骤(就像`yarn init`)或者进入 packages 文件夹，创建子文件夹并单独设置每个包。

```
lerna create <name> 
```

Enter fullscreen mode Exit fullscreen mode

当然，使用 Lerna 命令，您根本不需要创建目录或转到 packages 文件夹。但是，我仍然更喜欢使用标准方法，因为`lerna create`另外为你设置了一些样板文件，对我来说，这不是我想要的。😕

现在你已经准备好你的包裹了。在它们中的每一个中，你只是继续创建一个结构，就像你通常在一个单独的包中做的那样。但是如果这些包应该共享一些东西呢？例如，您可能希望它们中的每一个都以相同的方式与相同的工具捆绑在一起。为此，我们将在 monorepo 的根目录下，在 **[bili.config.js](https://bili.egoist.sh/#/configuration-file)** 文件中设置我们的毕丽配置。

但在此之前，先说一下毕丽。毕丽只是一个不错的、基于汇总的零配置(默认)捆绑器，内置了对 **ES-Next** 和 **CSS** 的支持**。我发现当不想从头开始配置 Rollup 时，这是一个非常好的选择。即便如此，毕丽在配置自身和底层汇总时仍然提供了相当多的选项(例如[添加插件](https://bili.egoist.sh/#/plugins))。也就是说，在我们的例子中，适用于毕丽的一切都可以适用于仅汇总配置。**

现在，我们应该更深入地看看配置文件中的路径。考虑下面这个相当完整的设置:

```
// bili.config.js
// ...

module.exports = {
  input: "./src/index.ts",
  output: {
    moduleName: "Package",
    minify: true,
    format: ["umd", "esm"],
    dir: "./build"
  },
  // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

从之前的一些配置文件中，你可能知道 Node.js 内置的`path`模块和提供的`__dirname`变量被使用。这里，区分`__dirname`和**相对路径**(总是从`./`开始)很重要。我们的配置文件位于 monorepo 的根目录下，而毕丽将分别运行在不同的子文件夹中。这是 Lerna 提供给我们的一个很好的功能，我们马上就会用到它。但是现在，重要的是让我们的配置工作。因此，`__dirname`变量引用给定的**文件所在的目录**，而以`./`开头的路径引用相对于当前在上工作的**路径的目录。这是需要注意、记住并在我们的配置中使用的，稍后将被不同目录下的多个包使用。**

## 打字稿

```
// bili.config.js
const path = require("path");

module.exports = {
  // ...
  plugins: {
    typescript2: {
      cacheRoot: path.join(__dirname, ".rpt2_cache"),
      useTsconfigDeclarationDir: true
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在其 [**官方文档**](https://bili.egoist.sh/api/interfaces/config) 中找到所有其他毕丽选项的文档。这里，我只想谈谈插件属性，我们将使用它来支持**类型脚本编译**(正如我所承诺的)。您可能还记得，我们之前安装了一个带有 typescript2 后缀 的 [**Rollup 插件，以及所有其他开发依赖项。而且，有了这个后缀，你可以让毕丽使用我们的插件选择，并自由配置它。请注意，安装后的 typescript2 插件在默认情况下不受支持，无需进一步配置。但是在这里，我想改变 2 个**](https://github.com/ezolenko/rollup-plugin-typescript2)**[选项](https://github.com/ezolenko/rollup-plugin-typescript2#plugin-option) - `cacheRoot` -只是为了我们的缓存不单独位于每个包中，而是位于根中(美学原因💅)——另一个长的在 **tsconfig.json** 指定的目录下输出我们的 TS 声明文件。**

说到 tsconfig.json，我们也应该为它设置一个特殊的设置！但是这次会有点复杂。在我们的**根目录**中，我们将设置我们的**基本配置**，供其他包范围的配置继承。

```
{  "compilerOptions":  {  "module":  "esnext",  "lib":  ["esnext",  "dom"],  "strict":  true,  "declaration":  true,  "esModuleInterop":  true,  "moduleResolution":  "node"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在每个包的目录中，我们需要创建一个单独的 tsconfig.json 文件，在其中我们将放置所有与路径相关的选项。比如:

```
{  "extends":  "../../tsconfig.json",  "exclude":  ["node_modules",  "tests"],  "include":  ["src/**/*"],  "compilerOptions":  {  "declarationDir":  "./typings"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

有了所有这些，我们应该有一个好的**毕丽+打字稿**设置，将包输出到每个包的**构建**目录，并将打字输出到**打字**目录。不错！😎

## 用法

现在，为了完成我们的 monorepo 设置，是时候测试它了！为了将我们的代码捆绑到每个包中，我们可以使用 [`lerna exec`](https://github.com/lerna/lerna/tree/master/commands/exec#readme) :

```
lerna exec -- bili --config ../../.bilirc.js 
```

Enter fullscreen mode Exit fullscreen mode

主命令后的两个破折号(`--`)允许即将到来的参数传递给正在执行的命令，而不是传递给 Lerna。现在，我们所有的包都应该被正确地捆绑在一起。

但是，不需要反复输入相同的方法。当然，您可以将上面的`lerna exec`命令添加到 root package.json 中的`scripts`属性，但是我有一个更好的解决方案。比方说，你对每个包有不同的**构建脚本**(在我们的配置中并非如此，但无论如何)，你仍然希望能够用一个**命令**运行所有的脚本。为此，您可以在每个包的 package.json 中提供单独的`build`脚本，就像这样(`watch`只是一个不错的补充👍):

```
{  ...  "scripts":  {  "build":  "bili --config ../../.bilirc.js",  "watch":  "bili --watch --config ../../.bilirc.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

毕竟脚本已经设置好了，你可以用 [`lerna run`](https://github.com/lerna/lerna/tree/master/commands/run#readme) 命令:
运行你所有包里的所有脚本

```
lerna run build 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让`lerna run`、`lerna exec`或其他 Lerna 的命令(如[、`lerna add`、](https://github.com/lerna/lerna/tree/master/commands/add#readme))只应用于某些包，你应该使用过滤标志，例如[、`--scope`、](https://github.com/lerna/lerna/tree/master/core/filter-options#--scope-glob)或[、`--ignore`、](https://github.com/lerna/lerna/tree/master/core/filter-options#--ignore-glob)。当传递了您的包的名称(在各自的 package.json 文件中的名称，而不是目录名称)时，这些将正确地选择要应用给定操作的包。

使用 Lerna 时，您必须知道的最后一件事是如何使您的 monorepo 包相互依赖。这也很简单！只需将你的包的名字添加到给定的 package.json `dependencies`列表中，然后运行 [`lerna bootstrap`](https://github.com/lerna/lerna/tree/master/commands/bootstrap#readme) 就可以正确地对它们进行符号链接和设置。

```
lerna bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

# 的摇滚！

我可能会说，我们几乎没有触及表面，但今天也学到了很多东西。当然，Lerna 还有一些我们没有谈到的命令，主要与管理 **NPM 发布**和**发布**有关。但是，就目前而言，我们谈到的 monorepo 设置已经完成。现在你可以自由地开发你的 monorepo，并在你被阅读时考虑发布管理。有了这样的**伟大的明面**，应该不会花你太长时间。😉然后，记得去官方(其实也很好) [**Lerna docs**](https://github.com/lerna/lerna) 了解更多。

# 单反...😵

那么，你怎么看待这篇文章和 monorepos 本身呢？你喜欢这个想法吗？更重要的是，你喜欢这个职位吗？😅在下面的评论区写下你的想法**！哦，还有**如果你想的话留个反应**！**

一如既往地考虑在 Twitter 上**关注我** [**，在我的脸书页面**](https://twitter.com/areknawo) 关注**最新内容**。另外，如果你愿意，可以去看看我的个人博客**。再次感谢你阅读这篇文章，祝你有美好的一天！🖐****