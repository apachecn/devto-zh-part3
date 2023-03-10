# Windows 上的 Web 开发

> 原文：<https://dev.to/statebait/web-development-on-windows-4d64>

在普遍的观念中，Windows 并不倾向于在任何类型的开发上进行 web 开发。但老实说，随着 Windows 成熟到今天的样子，这些年来这种情况已经改变了。

> 本文假设你使用的是 Windows 10，然而由于 Windows 提供了相当多的向后兼容性，你应该也能在旧版本的 Windows 上实现这一点。

## 终端

让我们从终端开始。终端将被广泛使用，因为您将使用各种不同的 CLI(命令行界面，如 npm)。首先，你应该使用 PowerShell 和 bash(我将谈到这一点)以及类似于 [hyper](https://hyper.is/) 的包装器。Hyper 是基于 HTML/CSS/JS 的电子终端。它有大量的扩展来增加实用性和主题来丰富你的体验。

[![terminal](img/34bf0f9129f6c7b71fdd942ce8e807c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9kWm4Qr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/x48egesbs9hf6i5g1cdh.jpg)

我已经提到了 bash 的使用。是的，你可以在 windows 上安装 bash，不，这不是一个噱头或一些半生不熟的东西，这是真的。了解 Linux 的 Windows 子系统(该功能被添加到 Windows 10 的一个较新版本中)。[这里](https://docs.microsoft.com/en-us/windows/wsl/install-win10)是怎么安装的。每当 PowerShell 给你带来不必要的麻烦时，你应该使用 bash。请记住，当您运行 bash 时，它是在您的 windows 上运行的完全不同的操作系统，因此您需要使用适当的命令对所有 CLI 进行全新安装(取决于您选择的 Linux 安装)。

## 方便的工具

接下来让我们安装一个命令行安装程序。这对于快速安装不同的工具非常方便。windows 上最受欢迎的两个选项是[勺子](https://scoop.sh/)和[巧克力](https://chocolatey.org/)。我个人更喜欢 Scoop，因为它的操作更简单，但是两者都不会错(安装两者也是一种选择)。这些可以让你安装节点，Python 等。眨眼之间。

接下来，我们需要挑选一个好的 web 包管理器。同样，最受欢迎的两个是 npm 和 yarn。安装 Node.js 时会默认安装 npm，你可以在这里安装 yarn。Yarn 使用 npm 的包存储库，唯一的区别是它处理依赖关系的方式和它的性能(你可以在它的网站上阅读更多关于这些区别的内容)。我个人使用纱线，但这两种都不会错。还有一个包管理器现在不用了(但是维护了)，不过我觉得还是要提一下；鲍尔。

安装 Git 也是必要的，因为它是大多数现代 web 项目和任何开发项目的主干😜([此处](https://medium.com/code-dementia/why-and-how-you-should-start-using-git-github-right-now-28617df72545)是 Hemant Jain 关于 Git 和 GitHub 的指南)。最后，由于缺少构建工具，您可能会遇到一些错误。这些是[窗口构建工具](https://www.npmjs.com/package/windows-build-tools)，是编译、运行和构建不同东西所必需的一些模块。虽然我不清楚这个包具体包含哪些模块，但它帮助我在尝试新东西时避免了许多错误，并且对于许多不同的项目都是强制性的。你可以通过运行
来简单地安装它们

```
npm install --global windows-build-tools 
```

或者，如果你用的是纱线:

```
yarn global add windows-build-tools 
```

## 代码编辑器

代码编辑器是 web 开发中最重要的事情之一，事实上在任何类型的开发中都是如此(是的，我有重复短语的习惯)。在编码过程中拥有无缝体验，调试有助于克服随之而来的所有挫折。就我个人而言，我使用过很多不同的代码编辑器，有些加载了过多的特性，有些只有最少的特性。然而，只有一个坚持；VS 代码(Visual Studio 代码的缩写)。VS 代码附带了一些现成的特性，这些特性应该足以让您入门，并且随着您的深入，您可以探索其巨大的扩展市场来增强您的体验。带有一组好的扩展的 VS 代码可以很容易地复制一个现代的成熟的 IDE，并且所有这些都具有出色的性能。目前，它是许多不同类别的开发人员最受欢迎的代码编辑器，每个月都会收到新的更新和功能。

[![vscode](img/aee6fb2f593ec9c0fc6838f9fa90a4d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xLH3X7Zp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/u9nbdql01o3xeyf4eswv.png)

足够的 VS 代码，继续我们还有其他的选择，比如 [Atom](https://atom.io/) ， [Sublime](https://www.sublimetext.com/) ，[括号](http://brackets.io/)和 [WebStorm](https://www.jetbrains.com/webstorm/) 等等。Atom 与 VS 代码非常相似，但它主要缺乏的是性能，当您在 Atom 中加载新的扩展时，它会明显变慢。WebStorm 是一个行业级的 IDE，具有开箱即用的所有重要功能，但它不是免费的(除非你是一名在校学生)。

或者，您也可以使用云 IDE，它基本上是在浏览器上运行的代码编辑器。它们有很多方便的功能。其中有[代码沙箱](https://codesandbox.io/)、 [GitPod](https://www.gitpod.io/) 和[编码器](https://coder.com/)等。

## API 测试仪

拥有一个 API 测试器(针对 REST 和 GraphQL API)非常重要，因为当您在后端为应用程序开发不同的 API 时，测试它们最简单的方法就是这些。最受欢迎的两个是[邮递员](https://www.getpostman.com/)和[失眠](https://insomnia.rest/)，后者也支持 GraphQL。我个人使用失眠症是因为它的界面更容易使用，并且有免费的主题。另一方面，Postman 有很多不同的团队特性，当你和大型团队一起处理大型项目时，这些特性会很有帮助。他们两个都不会错的。

## UI/UX 设计

不管你喜不喜欢，用户界面和用户体验是 web 开发不可或缺的一部分。当开发任何 web 项目的前端时，有时从创建界面的模型和原型开始会有很大帮助。一些简单(并且免费)的工具有 [Figma](https://www.figma.com/) 和 [Adobe XD](https://www.adobe.com/in/products/xd.html) 。我更喜欢使用 Figma，主要是因为它有一个选项可以轻松导出任何创建的对象的 CSS。除此之外，你两个都不会错。

## 结论

在 Windows 上开发 Web 从来没有像今天这样简单，所以不要再相信只有 Mac 和 Linux PC 才能做到，现在就开始在 Windows 上开发吧。😃