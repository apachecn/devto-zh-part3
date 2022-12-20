# 前端工程师普通人的合理工具

> 原文：<https://dev.to/igneel64/sane-tooling-for-the-frontend-engineer-commoner-2jan>

*注意:这个指南没有太多的大惊小怪和闪亮的东西，它专注于在你的日常项目中变得最有效率，让你自己和你的团队快乐*

## 为什么要看这个指南？

在现代 JavaScript 环境中，当您想要启动/跳转到一个**项目** / **特性**时，最初花费在工具方面的时间比实际实现的时间要多。这些工具可能从 linters 和 beautifiers，到特定的代码编辑器和插件。有大量的选择和无限量的组合，有人可能会选择采用。但是所有这些工具都有自己的特点和学习曲线，需要相当长的时间来熟悉，从而变得有效。

这清楚地表明，如果我们希望跨团队和组织采用一个公共的质量基线和“我们工作的方式”,我们需要在工具方面至少有一些合理的缺省。下面的命题并不严格，但它们可以作为一个很好的起点。同样，新的添加和增强应该非常受欢迎。

## 盒子里是什么？

*   带插件的代码编辑器
*   美化和林挺
*   去吧胡克斯
*   代码质量规则

## 代码编辑器

作为起点，我们选择的编辑器是 [VSCode](https://code.visualstudio.com/) 。这是一个编辑器，顾名思义不是一个成熟的 IDE，但是有了所有的插件和社区支持，它可以很快变得像 IDE 一样。该编辑器提供的一些现成好处包括:

*   轻量级(开始时)
*   可定制的
*   主要集中在 JavaScript 和 friends 的开发上
*   可扩张的
*   与 git、调试、智能感知、命令行界面等的内置集成。

正如你也能很快理解的那样，它得到了微软的支持，所以没有成为被排除在外的项目的危险。社区很棒，正在进行的开发工作真的令人兴奋。我预测，如果还没有的话，它将成为大多数*通用*开发工作的事实(除了 Java，IMHO 还没有准备好)。

### 分机

这个编辑器的一个核心特性是，你可以在不离开编辑器界面的情况下找到并下载大量的扩展，大多数时候甚至不需要重新加载应用程序。

我已经向你保证过，这个指南将只包括明智的选择，没有 T2 式的废话，所以我会很快的。

*   [现场分享](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare-pack):对我来说，这个扩展是你不仅应该融入编辑器，还应该融入你的团队*文化*的第一件事。你可以通过它共享编辑器界面、文件结构、控制台甚至本地开发服务器。在我的公司，我们广泛使用配对、演示和代码检查。
*   [记录这个](https://marketplace.visualstudio.com/items?itemName=joelday.docthis):即使我们想要遵循我们伟大的类、函数和参数的命名惯例，我们都可以熟悉这样一个事实，即检查具有清晰和熟悉的文档的代码真的很好。这会节省你很多时间。*也请阅读[这篇文章](https://medium.com/@trukrs/type-safe-javascript-with-jsdoc-7a2a63209b76)以了解如何在 VSCode 中仅用 JSDoc 注释在常规 JS 上集成类型安全。*
*   埃斯林特 & [更漂亮](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode):这些也是我们美化和林挺规则的主要支柱。VSCode 对这两者都有一流的支持。
*   GitLens :迄今为止我发现的最好的 git 扩展。
*   [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost) :每次您将一个外部依赖项导入到您的项目文件中时，这个扩展将向您显示您在应用程序的依赖树上添加的大小(以 kb 为单位)。这可能看起来是一个“很好的东西”，但当进入生产级别时，这些东西很重要。就我个人而言，使用这些东西已经有一段时间了，如果没有它，我会觉得有点赤裸。
*   [TODO Highlight](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight) :我个人觉得很有趣的扩展，为你的评论提供了丰富的补充。真的很酷，你可以列出你所有的代码。

我们所坚持的技术的扩展:
让我们不要忘记，我们中的许多人将会在一个带有历史印记的应用程序中工作。比如我们的是 CoffeeScript，对你来说可能是 Jinja 什么的。但我们不会犹豫不决，也不会对当时不得不做出看似合理的决定的同事咆哮。所以我们继续安装

*   [咖啡棉绒](https://marketplace.visualstudio.com/items?itemName=lkytal.coffeelinter)

## 美化&林挺

对于林挺，我们选择 [ESLint](https://eslint.org/) 作为我们项目的默认代码 linter。ESLint 是最受欢迎的、经过实战检验的、也是可扩展的 JavaScript linters。我建议你(和你的团队一起)定义一套规则，这套规则可以适用于你将要承担的大多数项目，然后就像我们对[自己的](https://www.npmjs.com/package/eslint-config-welcome)所做的那样，将它作为一个 npm 包进行安装。

ESLint 插件设置可以被增强，以便与美化工具的使用链接在一起，并且该工具[更漂亮](https://prettier.io/)。这使得我们永远不必担心在我们的代码库中有相同的样式约定。

没有什么比审查包含样式更改的拉请求更令人困惑和不舒服的了。
要配置美化规则，可以在代码库顶层的. prettierrc 文件上添加选项。我们团队在大多数项目中使用的配置示例如下:

```
{  "trailingComma":  "es5",  "tabWidth":  2,  "semi":  true,  "singleQuote":  false,  "bracketSpacing":  true,  "arrowParens":  "always"  } 
```

这些工具与 VSCode 的集成可以配置为在我们开发时运行，警告我们危险/错误的编码实践，或者在保存文件后自动格式化我们的代码。(强烈推荐)

## Git 挂钩

由于我们已经在项目中集成了自动林挺和美化，如果我们不小心提交了不符合这些标准的代码，那将是一种耻辱。为了确保这一点，我们设置了特定的 git 挂钩，允许我们在每次提交、推送或版本控制生命周期中的任何交互之前运行自定义过程。为了以最简单的方式设置这种自动化，我们使用了 [husky npm 包](https://www.npmjs.com/package/husky)，它允许我们直接从 package.json 文件配置 git 挂钩。
现在，这些钩子确保预提交代码是格式化的，但也不允许任何被 ESLint 认为是错误的代码。这意味着向存储库提交代码必须遵循我们项目的编码标准。

## 代码质量

所以这是一个困难的问题，因为大多数人认为任何规则集都可以保证代码质量。但我认为我们可以弥补一些事情，从长远来看，这些事情会让我们省去很多麻烦。我发现最有用的东西是基于一个叫做[复杂性](https://eslint.org/docs/rules/complexity)的术语的规则，它可以通过 ESLint 包含进来。我建议你多看看这个概念，我通常不喜欢它，但是[维基百科关于这个的文章](%C2%A0https://en.wikipedia.org/wiki/Cyclomatic_complexity)非常好。这就是我们正在使用的规则集:

```
{
    "complexity": [2, 5],
    "max-statements": [2, 7],
    "max-statements-per-line": [ 2, { "max": 1 } ],
    "max-nested-callbacks": [2, 2],
    "max-depth": [ 2, { "max": 2 } ]
} 
```

设置这些规则是为了防止我们提交难以测试、理解、审查和推理的代码。这些陈述最初可能看起来有点严格，有时真的很麻烦，但我们不应该动摇。除了在我们日常使用的工具上测试我们自己的能力和技能以变得更好，我们还可以在其他社交平台上吹嘘它。

## 一切都是为了效率

任何人都可以争辩说这是另一个设置。确实是这样，但是在我们的团队中，我们发现它结合了我们可以得到的最好的东西，以充分利用我们的开发经验，尤其是在工作中。我希望你能从中得到一些东西，我真的希望你能在评论区提出建议和反馈。