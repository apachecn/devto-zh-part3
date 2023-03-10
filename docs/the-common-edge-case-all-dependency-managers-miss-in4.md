# 所有依赖关系管理器都忽略的常见边缘情况

> 原文：<https://dev.to/jules2689/the-common-edge-case-all-dependency-managers-miss-in4>

Ruby 的 Bundler & RubyGems，Mac 的 Homebrew，Linux 的 Apt(高级包工具)& Debian 包，Javascript 的 NPM & Yarn，Python 的 Pip，以及几乎所有其他依赖项安装程序或管理程序除了处理依赖项之外还有什么共同点？它们都不能处理安装依赖项的所有边缘情况，因为它们“天真地”管理依赖项。

这篇文章的目的不是羞辱或贬低这些不可思议的软件所做的工作(我自己也为 Bundler 做了贡献，并且[还会继续做下去！](https://github.com/bundler/rfcs/pull/17))。我们拥有的依赖项管理器为我们提供了一个非常有价值的服务，我非常感谢他们。然而，这篇文章的目的是解开这种幼稚的依赖性管理的陈述，向您展示一种替代方案，并探索其中的差异。

这篇文章针对的是对依赖管理有一点了解的开发人员，但它尽最大努力沿途解释概念(如果你有什么不明白的地方请评论，我会尽最大努力解释)。对于对依赖管理有更透彻理解的人来说，这篇文章可能不会太简单。所以，这篇文章的目标是一般的开发者。虽然我倾向于使用更多基于 Ruby 的例子，但是这个概念同样适用于其他语言和软件，比如 Javascript、Java、Python、Linux 和 Mac。

尽情享受吧！

## 依赖系统是如何工作的？

首先，让我们从这些依赖项安装器/管理器是如何工作的开始。它们都以大致相同的方式工作:

1.  获取一些[依赖项]的索引/列表
2.  基于所需依赖项的一些定义，执行依赖项解析算法，旨在消除重复的子依赖项，并找到匹配每个需求的版本
    *   可以以各种方式导出所需依赖项的列表，例如文件(如`package.json`、`requirements.txt`、`Brewfile`和`Gemfile`)或命令(如`yarn install foo`、`gem install foo`或`apt-get install foo`)，
3.  安装从(2)得到的依赖项列表

这些依赖项安装到全局或本地命名空间中，可能链接到静态系统依赖项，并且可能依赖于安装依赖项时的系统状态。

#### 举例

让我们来看看这个系统的一个例子。

[![Simple Dependency System at play](img/ef3e8cafff6cc211887bac553dc5fa00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2krwim0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5fx4wclrmpmdh5e2syw3.png)

在这个高度简化的图表中，我们看到当我们在处理`my_app`时，我们依赖于 Ruby、MySQL 和`Rmagick`。MySQL 预计在`/usr/local/bin/mysql`并指向 MySQL 5.7，`RMagick`取决于`ImageMagick@6`，后者取决于`pkg-config`和`freetype`(等等)。

理论上，这个系统运行良好。有了清晰的有向依赖图就很简单了。然而，这是一个理想的场景，很少是真实世界的结果。

当您同时运行多个应用程序时，系统会出现问题。想象一下`my_app`和`another_app`都依赖于 MySQL 的场景。前者依赖于 MySQL 5.7，后者依赖于 MySQL 5.5。不幸的是，所有的依赖都假设 MySQL 在`/usr/local/bin/mysql`。这个路径通常被硬编码成许多依赖项，或者我们使用由某个全局变量设置的相同目录，并期望二进制文件被称为`mysql`。这个事实意味着我们一次只能运行一个版本，而不需要对全局设置的环境变量进行笨拙的处理。

我们可以做的是让 MySQL 5.5 成为一个不同的二进制名称(例如`/usr/local/bin/mysql_55`)，但是大多数包和系统期望`mysql`而不是`mysql_55`，所以这也导致了许多问题。

这个场景意味着我们需要一次更新所有依赖的应用程序，以便能够更改 MySQL 的版本。随着我们有更多的应用程序/服务，这个问题变得更加严重，因为它发生的机会更多，依赖性重叠的机会也更多。

[![A more real-world dependency graph](img/ca9375c0e80ad7b32b92d147b6f12d41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pzZGtXh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uxvjayfumulsqdqhmfke.png)

## 这是什么意思？

这个问题在大多数现代依赖关系管理器中普遍存在。我们犯了一个错误，假设一个软件一次只能运行一个版本——或者可能是故意简化。我怀疑我们之所以走到这一步，是因为服务器，尤其是容器化的服务，通常意味着一个系统只运行一个应用程序。但是，在您自己的计算机上，您可能同时运行多个应用程序。

我在系统依赖 ImageMagick(编程图像处理软件)、MySQL(数据库软件)和 Readline (Ruby、Python 和其他依赖于此的语言)时，看到过很多这样的问题。当 Readline 改变时，你可能必须重新编译所有版本的 Ruby 和 Python，以及所有安装的 gems/eggs/wheels/dependencies)。

您可以通过使用`docker-compose`这样的模式来缓解这个问题。`docker-compose`模式将所有代码放入一个容器中，这样多版本问题就不会轻易发生。然而，这种模式意味着您的编辑器现在必须能够在生成的 docker 实例中工作，或者您需要将您的文件同步到您的本地机器上(我在管理这种模式的数百个实例时看到过这种情况，这导致了许多同步问题和对真实来源的混淆)。

所以没有`docker-compose`(这还能打这种边缘情况！)，我们谈到了由于命名的原因，我们不能在*同一个地点*有重复的版本，但是我们*可以在不同的地点*有多个版本。问题变成了管理这些不同的点，并告诉依赖者如何在那个环境中运行。

有一个系统考虑了依赖性管理的所有边缘情况，并且可以处理不同的点，称为 Nix。

# 定期发布前的一点序文

我们刚刚讨论了在大多数现代依赖系统中，依赖管理是多么的幼稚。我相信这是过去的遗留物，那时系统的依赖性很少，保存这些依赖性的硬盘空间也很少。

随着依赖项的数量和依赖项的大小不断增长，将它们都保存在 20 世纪 90 年代的同一个小硬盘上是不可行的，当时开发了一些旧的依赖项管理软件，如 1998 年的 Linux Apt(高级包工具)、2003/2004 年的 RubyGems (Ruby)、1998 年的 DisUtils (Python)和 2003 年的 PyPi (Python)。硬盘大小问题是我的一个理论，我不会深究，但我觉得它对 15-25 年前的决定有一些共鸣。我还怀疑依赖关系管理人员这些年来都在“复制”彼此，而没有重新评估潜在的依赖关系理论，或者故意保持简化。尽管如此，*我们的依赖管理器确实为我们提供了一个无价的服务，我非常感谢他们*，但是我确实认为还可以有一些改进。

## 什么是 nix？

Nix 是一个“功能性的”和“纯粹的”包管理器这意味着它在纯函数式编程语言(如 Haskell)中将包视为值。Nix 的这些属性意味着包是由没有副作用并且不能改变的函数构建的。

这种方法与家酿、NPM 和 RubyGems 等系统不同，如果您安装了各种其他软件、特定位置的特定软件包或特定环境变量集，这些系统可能会以不同方式安装软件包。

取而代之的是，Nix 依赖于一个系统来构建一个完整的依赖项目录，并用一个构造好的散列来对依赖项进行版本化。散列是通过考虑用于构建包的所有东西而构建的，因此我们可以保证它对于任何构建设置都是唯一的。然后，Nix 不是直接引用 MySQL，而是通过符号链接引用散列副本。这种设置意味着我们可以引用相同的二进制名称(例如，我们已经看到`mysql`是必需的)，但它会根据您使用的应用程序指向不同的版本，在某些情况下，不同的依赖项会指向不同的版本。您可以在下图中看到这一点:

[![Nix-ified My_app dependency graph](img/a924b24a445e53706f3f940e5aed70d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nJ1Ih6sH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ewu0ry0bbusowjrpbda5.png)
[![Nix-ified Another_app dependency graph showing different versions of the same dependencies](img/5fb709fc0ac2708c539a16f1d51daee8.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--lE3TRCLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnhycsfwptq32uq7rx64.png)

### 纯包裹

当我说“纯包”时，我的意思是包不受指定之外的任何东西的影响，这意味着环境变量、其他系统依赖项，甚至您的`HOME`和`TMPDIR`目录都不会影响最终的依赖项。

在 Linux 上，依赖关系是在系统的一个虚拟隔离区域中使用所谓的派生来构建的。在 Mac 上，您无法访问`TMPDIR`或`HOME`，这两个环境变量都被设置为不存在的点。同样，`PATH`是空的，它告诉你的系统在哪里可以找到依赖项，所以你不能访问你预先安装的依赖项。

您不再依赖于系统中先前的内容，而是确切地指定运行和构建所需的内容。Nix 只使用以前安装在 Nix 系统中的东西，当且仅当计算出的散列(决定它是否兼容)与请求的依赖项的散列匹配时，否则 Nix 构建一个新的。

这种方法创建了一个保证无冲突的非循环依赖图，并且是“正确地”处理依赖管理的所有边缘情况的方法。

## 怎么办？

那么，你能做什么？老实说，现在没有一堆工作你可能做不了多少。然而，我希望这能帮助你理解你所经历的依赖冲突。除非你愿意花时间切换到`nix-os`(一个 Linux 发行版)、`nix-shell`(一个处理激活适当依赖项的子 shell)，或者编写你自己的与 Nix 的集成，否则你可能没有什么可以改变的。

也就是说，如果你在一个有许多相互关联的服务的大型组织中工作，那么解决这些需求可能会变得更加紧迫，Nix 可能是一个好的解决方案。作为一个参考点，在我曾经工作过的一家公司中，大约有 1000 名成员在 RnD 团队中，50%的内部开发人员支持问题都与依赖管理有关。

## 最终字

我们已经看到了大多数现代依赖性管理系统如何不处理依赖性管理中的边缘情况。当一个依赖项有两个不同的和不同的需求时，这种边缘情况就会发生，导致冲突，因为我们一次只能使用一个。然后，我们研究了 Nix，以及它如何使用一个独立的构建系统和一个功能链接的系统来解决这个问题。我不认为现在任何人都有太多的事情要做，不需要大量重写依赖系统，但是我们可以改进错误消息！

我希望适当管理的依赖关系管理在未来会有所改进，变得更加简单，更加平易近人。目前，如果你正在编写一个依赖关系管理器，请考虑在依赖关系冲突时使错误消息更加清晰，并提供更好的错误消息/教育(使用我在 2018 年 RubyKaigi 会议演示中描述的方法)！).

* * *

#### 资源

如果您想了解更多关于 Nix 的内容，这里有一些资源:

*   [http://notes.burke.libbey.me/learning-nix/](http://notes.burke.libbey.me/learning-nix/)
    *   我的朋友写了一堆关于尼克斯的东西。挺有帮助的！
*   [https://nixcloud.io/tour/](https://nixcloud.io/tour/)
    *   这个链接是 Nix 函数式语言的一个教程。这是一次非常棒的旅行，除了学习 Nix 语言之外，还可以用来学习一种很好的教学方法。
*   [https://nixos.org/nixos/nix-pills/](https://nixos.org/nixos/nix-pills/)
    *   这是几十种一口大小的“药丸”，可以让你摄入小剂量的 Nix 来学习
*   [https://nixos.org/~eelco/pubs/phd-thesis.pdf](https://nixos.org/%7Eeelco/pubs/phd-thesis.pdf)
    *   Nix 来自一位荷兰计算机科学家的博士论文。他们的重点是建立一个数学上和逻辑上合理的系统，我认为他们做到了

#### 其他有用的链接

*   纽交所打包回购:[https://github.com/NixOS/nixpkgs](https://github.com/NixOS/nixpkgs)
*   Nix OS Manual: [https://nixos.org/nixos/manual/](https://nixos.org/nixos/manual/)
*   Nix 软件包手册:[https://nixos.org/nixpkgs/manual/](https://nixos.org/nixpkgs/manual/)
*   一个非官方的 nix wiki:[https://nixos . wiki](https://nixos.wiki)

* * *

感谢阅读！