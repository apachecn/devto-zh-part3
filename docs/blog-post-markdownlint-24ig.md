# 博文:Markdownlint

> 原文：<https://dev.to/jonasbn/blog-post-markdownlint-24ig>

作为我 11 号帖子的后续。在哥本哈根演讲“我的演讲被接受了。所以在哥本哈根二月的会议上，我做了一个关于 **Markdownlint** 的报告。这些幻灯片可以在[的 SlideShare](https://www.slideshare.net/jonasbn/markdownlint) 上找到，但是我想我应该在这里写一篇关于演讲主题的小文章。

首先介绍一些*的基本*背景历史关于 **Markdownlint** 目前有两个实现，第一个在 **Ruby** 中实现，灵感来自于**节点**中的实现。**节点**库另外还有一个带有命令行接口(`markdownlint`)的 NPM 包，其中 **Ruby** 包带有命令`mdl`。

基于**节点**的实现是我日常使用的，所以我的重点将放在这一个上。我现在主要在 **Visual Studio 代码**中工作，因此使用那里可用的插件和命令行工具`markdownlint`，两者都基于**节点**实现。升华文本和原子的插件都存在。

现在我们来看一些细节。linters 都指定了一套规则，当你的**降价**没有遵守建议的最佳实践时，可以应用这套规则。所有的规则都由一个键来标识:`MDXXX`，其中的数字标识了一个独特的部分，但是你不必使用并记住所有这些*神秘的键*，你也可以使用人类可读的别名。

为了我的演示，我准备了一个小的 [GitHub 库](https://github.com/jonasbn/markdownlint-presentation)，里面有所有使用的参考资料和一些粘贴和复制的例子，因为幻灯片并不总是以后最好的参考资料。

因此，如果我们从提到的库中提取[的例子，它是`markdownlint` ( **节点**)的配置文件。](https://github.com/jonasbn/markdownlint-presentation#markdownlint-configuration) 

```
{  "default":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

这是`markdownlint`最基本的设置，它基本上启用了所有的规则。现在让我们回到规则上来。

例如，规则`MD013`也有别名`line-length`，这在例如配置文件中更容易使用。因此，如果我们想禁用我们的*林挺*的行长度规则，我们应该写如下:

```
{  "default":  true,  "line-length":  false  } 
```

Enter fullscreen mode Exit fullscreen mode

这也是示例 GitHub 存储库在编写时使用的配置:-)

上面给出的例子可以写到一个名为`.markdownlint.json`的文件中，这意味着命令行工具`markdownlint`将选择这个本地配置(如果可用的话)。

因此，如果我们再次启用`MD013` ( `line-length`)，我们将得到以下输出:

```
$ markdownlint README.md
README.md: 7: MD013/line-length Line length [Expected: 80; Actual: 129]
README.md: 13: MD013/line-length Line length [Expected: 80; Actual: 104]
README.md: 14: MD013/line-length Line length [Expected: 80; Actual: 130]
README.md: 15: MD013/line-length Line length [Expected: 80; Actual: 167]
README.md: 17: MD013/line-length Line length [Expected: 80; Actual: 139]
README.md: 18: MD013/line-length Line length [Expected: 80; Actual: 127] 
```

Enter fullscreen mode Exit fullscreen mode

为了做一个快速的比较，`mdl`也报告了同样的结果。

```
> mdl README.md
README.md:7: MD013 Line length
README.md:13: MD013 Line length
README.md:14: MD013 Line length
README.md:15: MD013 Line length
README.md:17: MD013 Line length
README.md:18: MD013 Line length

A detailed description of the rules is available at https://github.com/markdownlint/markdownlint/blob/master/docs/RULES.md 
```

Enter fullscreen mode Exit fullscreen mode

命令行是不可思议的，但是如果你像我一样使用 **Visual Studio 代码**,你将会大开眼界。代码的**插件基于相同的实现，因此你的项目可以在你的编辑器中使用`.markdownlint.json`配置。**

这需要[Markdownlint 插件](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)。下面的屏幕截图展示了关于相同违规行为的报告`MD013`

[![Visual Studio Code with Markdownlint](img/db614f1e7dfeb8e9a4dddf42f9ec1232.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zNhii6Mt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jonasbn/markdownlint-presentation/mastimg/vscode_with_markdownlint.png)

为了在顶部添加更多的*糖，请查看配置处理的反应。*

[![Visual Studio Code with Markdownlint JSON configuration file](img/7862273a814c3e84b55149c7bddb7007.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WKIaJAYP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jonasbn/markdownlint-presentation/mastimg/vscode_with_markdownlint.json.png)

该插件内置了对配置文件的处理，所以如果你将*悬停在*规则上，无论它是被指定为`MDXXX`还是相关的*别名*，都是可见的。这是非常方便的，因为它允许您编写人类可读的配置文件，但仍然有规则列表的漂亮的关键，可用于相应的减价广告。

但是并非一切都很好。这两个实现是*接近*，这意味着您可以在某种程度上互换使用这两个工具，但它们并不完全兼容。

**Ruby** 实现实现了从`MD001`到`MD041`的 38 条规则和一条规则`MD046`，而**节点**实现了从`MD001`到`MD045`的 41 条规则。

这意味着有问题的规则是:

*   `MD042`，仅在**节点的**中实现
*   `MD043`，仅在**节点的**中实现
*   `MD044`，仅在**节点的**中实现
*   `MD045`，仅在**节点的**中实现
*   `MD046`，仅可在**红宝石**中实现

你可以看到我对[红宝石](https://gist.github.com/jonasbn/c2ecef9bfc53d456b512e26f9e28dde6)和[节点](https://gist.github.com/jonasbn/bf2f4dbc86eccb6f446d10f729aab214)的两个要点。这当然可能会改变，但这是在撰写本文时的情况，请参考官方文档中的原始清单: [Ruby](https://github.com/markdownlint/markdownlint/blob/master/docs/RULES.md) 和 [Node](https://github.com/DavidAnson/markdownlint/blob/master/doc/Rules.md) 。

如果你意识到了差异，并且你的本地 **Markdownlint** 配置不依赖于任何提到的*漏洞*，你可以使用这两个工具，但是更容易选择两个中最适合你的情况的一个。因为使用两者都需要依赖单一的配置，所以维护规则成为项目中的额外工作。

除了启用和禁用之外，有些规则还可以有额外的配置参数，我还没有比较这两个实现的规则列表中所有这些可能的配置，所以在这方面可能也隐藏着一些问题。

除了命令行和编辑器集成，您可以将 **Markdownlint** 添加到您的 CI/CD 设置中，您可能不是您的**Markdownlint**产品的唯一贡献者，并且您不能假设其他人有类似的过程或相同的工具。

这是使用 **Markdownlint**
的 **Travis CI** 的一个非常基本的配置

```
language: node_js
node_js:
- '6'
install:
- npm install -g markdownlint-cli
script:
- markdownlint *.md 
```

Enter fullscreen mode Exit fullscreen mode

这适用于基于**节点**的项目，您也可以调整基于其他语言的项目的示例。

下面是一个基于 XML 的项目的例子:

```
language: node_js
node_js:
- '6'

before_install:
  - sudo apt-get install -y libxml2-utils

install:
   - npm install -g markdownlint-cli

jobs:
  include:
    - stage: "Documentation  test"
      name: "Markdown  test"
      script: markdownlint README.md
    - stage: "Product  test"
      name: "XML/XSD  test"
      script: xmllint --schema epp.xsd xml/*.xml 
```

Enter fullscreen mode Exit fullscreen mode

这里的产品是 XML，因此使用另一个命令行工具(`xmllint`)对*进行测试*，并使用`markdownlint`对文档进行测试。这两个例子都是从我的 [GitHub 库](https://github.com/jonasbn/markdownlint-presentation)中提取的。这使得这个美丽的表现。

[![Travis CI staged builds](img/8bfb89a0e3791c6ed7d459c7787aacf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kSTVsXqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jonasbn/markdownlint-presentation/mastimg/travis-ci-markdownlint.png)

仅供参考，这里有一个未经测试的示例，展示了使用`mdl`和 **Ruby** 的 **Markdownlint** 设置的 **Travis** 配置可能是什么样子。

```
language: ruby
install:
- gem install mdl
script:
- mdl *.md 
```

Enter fullscreen mode Exit fullscreen mode

在演讲中，我也谈到了什么是 linter，以及一般情况下使用 linter 的基本原理，我不想在这篇博客文章中触及这个话题，也许在以后的文章中。相反，我挖掘了更多关于 **Markdownlint** 实现的细节。在演示中，我还提到了 **CommonMark** 我决定不在这篇文章中触及这个话题，因为它会太长，这也可能是未来一篇文章的主题。

准备演示文稿和撰写这篇文章已经确定了几个可以进一步努力的领域(项目、贡献、PRs)。

*   在 **Ruby** 中实现`MD042`实现
*   在 **Ruby** 中实现`MD043`实现
*   在 **Ruby** 中实现`MD044`实现
*   在 **Ruby** 中实现`MD045`实现
*   在**节点**实现`MD046`

*   尝试更高级的 **Travis** 配置，以适应更多项目和库的变化，比如 **Rust** 、 **Perl** 等等

*   扩展**节点**和 **Ruby** 实现的功能，这样配置文件就可以被共享，然后很容易维护

*   最后，压轴戏——联系这两个项目的作者，建议合并 Markdownlint 和 rules 的标准，这样不同的实现可以根据作为标准列出的基线进行比较。这在很大程度上受到了为 **CommonMark** 所做工作的启发，并且不是完全不可撤销的，因为现有实现背后的令人敬畏的作者，以及事实上**节点**的实现受到了启发并且基于 **Ruby** 的实现。

我可能会将这篇博客文章中的很多信息传播到提到的 [GitHub 资源库](https://github.com/jonasbn/markdownlint-presentation)，但是请查看资源库中这篇文章使用的参考资料列表，初始演示和未来可能的发现、技巧和示例将放在那里。

快乐**降价**写作和林挺...