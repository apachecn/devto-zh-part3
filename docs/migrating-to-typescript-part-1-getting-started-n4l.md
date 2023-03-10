# 迁移到 TypeScript，第 1 部分:简介和入门

> 原文：<https://dev.to/resir014/migrating-to-typescript-part-1-getting-started-n4l>

*由[弗朗茨·哈文·阿西图纳](https://unsplash.com/photos/vkfrFrAIO4o)在 [Unsplash](https://unsplash.com/) 拍摄的头球图像。*

TypeScript (TS) 是一种近来迅速崛起的语言。在 [2018 年 JavaScript (JS)](https://2018.stateofjs.com/javascript-flavors/typescript/) 调查中，已经有了一些有利的结果。它甚至已经到了像[肯特·c·多兹](https://twitter.com/kentcdodds/status/1075853845048188929)这样的大牌开始向它迁移的地步。

要了解更多关于 TypeScript 类型系统是如何工作的，以及它如何帮助你，请观看 TypeScript 的创造者安德斯·海尔斯伯格的这次演讲。

[https://www.youtube.com/embed/hDACN-BGvI8](https://www.youtube.com/embed/hDACN-BGvI8)

对于我们许多已经在使用 TypeScript 的人来说，我们再也无法想象不用它来编写 JS 了。有了新增加的对 Babel 编译的支持，与 JS 生态系统的其他部分集成变得更加容易。但对于许多希望将应用程序迁移到其中的人来说，这可能会感觉有点难以承受。当你在看一个中型/大型的应用程序时，这变得更加失控，所有这些都是用 JavaScript 编写的。

很多 TypeScript 学习材料似乎从来没有深入研究如何将成熟的应用程序迁移到 TypeScript。更糟糕的是，TypeScript 确实有自己的官方迁移指南，但是已经过时了。

因此，在这一系列的文章中，我试图概述我个人关于如何将现有代码库迁移到 TypeScript 的步骤。第一部分将介绍为大规模重写准备项目的步骤。这包括设置 TS 编译器，以及 TypeScript 编译器的基本要素。

* * *

## 那么到底什么是 TypeScript 呢？

TypeScript 是编译成普通 JavaScript 代码的 JavaScript 的超集。它通过静态类型的强大功能实现了出色的工具和开发人员体验。静态类型释放的一些改进的 JS 体验包括更好的重构工具、语句完成等等。

TypeScript 由安德斯·海尔斯伯格撰写，他以 C#的首席架构师和 [Turbo Pascal](https://en.wikipedia.org/wiki/Turbo_Pascal) 的创造者而闻名。TypeScript 2.0 于 2016 年 9 月发布，具有大幅改进的 Node.js 模块支持和更严格的`null`检查。从那以后，这种语言不断得到改进，增加了一些特性，比如 object rest/spread、`--strict`模式、条件类型等等。【2018 年 7 月发布的 TypeScript 3.0 ，甚至通过项目引用支持 monorepos。

要阅读更多关于 TypeScript 的内容，我推荐 Basarat 的 [TypeScript Deep Dive](https://basarat.gitbooks.io/typescript/) 一书。

## type script 入门

因此，首先，我们需要为 TypeScript 设置我们的环境。有两种方法来设置它:

*   您使用 Babel 7 + TypeScript 预设进行编译，并让 TypeScript 编译器只进行类型检查。
*   您可以使用 TypeScript 编译器对代码进行类型检查和编译。

由于我们是从 JavaScript 迁移过来的，我们可以假设我们已经在开发工具链中使用了 Babel，所以我们可以选择第一个选项。也可以运行第二个选项，用巴别塔连锁。但是如果我们想更好地控制我们使用的巴别塔预置/插件，第一个选择还是更好的。

### 初始化编译器

本指南将使用 TypeScript 3.2。它应该可以在 3.0+以上的任何版本上运行。

要开始使用 TypeScript，请运行以下命令安装 TypeScript 编译器 CLI:

```
$ npm install -g typescript 
```

Enter fullscreen mode Exit fullscreen mode

然后运行`tsc --init`用默认选项初始化`tsconfig.json`文件。它列出了所有可用的选项和解释，不必要的选项被注释掉了。选项的数量可能会让您不知所措，但是让我们将配置分解为一些基本要素。

`tsconfig.json`

```
{  "compilerOptions":  {  "allowJs":  true,  "checkJs":  false,  "esModuleInterop":  true,  "downlevelIteration":  true,  "lib":  ["esnext",  "dom"],  "module":  "commonjs",  "noUnusedLocals":  true,  "outDir":  "dist",  "skipLibCheck":  true,  "strict":  true,  "target":  "esnext"  },  "include":  ["src"]  } 
```

Enter fullscreen mode Exit fullscreen mode

该设置将从`src`中取出所有内容，并将其编译到`dist`文件夹中。这里还有一些其他重要的编译器选项，但是我们将在下一节中讨论它们。要编译，运行`tsc`命令。

**注意:**如果用 webpack 编译东西，就不需要`outDir`选项了！

### 设置构建任务

既然 TypeScript 编译器工作了，我们可以将它作为脚本包含在我们的`package.json`文件中！

```
{  "scripts":  {  "build":  "tsc"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以简单地运行`yarn build`(或者`npm run build`，如果您正在运行 npm)来构建您的项目。

### 接通打字稿巴别塔预置(可选)

如果你已经使用 Babel 编译了你的 ES6+ JS 代码，你可以使用为 Babel 预设的 [TS。注意你需要**巴别塔 7 及以上**才能使用这个。](https://babeljs.io/docs/en/babel-preset-typescript) 

```
{  "presets":  ["@babel/preset-env",  "@babel/preset-typescript"],  "plugins":  [  "@babel/plugin-proposal-class-properties",  "@babel/plugin-proposal-object-rest-spread"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 编译器支持所有现代 ES2015 功能，以及下一代 ES 功能。尽管一个常见的缺陷是**你不能使用比 stage-3** 更新的下一代语法，因为 TS 不支持它。这意味着使用像[管道](https://github.com/tc39/proposal-pipeline-operator)这样的语法会给你带来类型错误。提案插件应该包括 TypeScript 翻译所需的第三阶段功能。

注意，巴别塔编译器**只**从你的代码中移除类型。**它不做任何额外的类型检查！**确保使用`tsc --noEmit`单独运行类型检查。或者更好的是，将它作为编译器选项添加到您的`tsconfig.json` :
中

```
{  "compilerOptions":  {  "noEmit":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

此选项将运行 TypeScript 编译器而不输出任何代码，因此它只运行类型检查。然后，您可以将`tsc`命令添加到您的`package.json`脚本中，如果您也使用 CI 系统，这将很有帮助。

```
{  "scripts":  {  "type-check":  "tsc"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果使用 Flowtype，则不能将 Flowtype Babel 预设与 TypeScript 预设一起使用。你必须选择其中一个！

## `tsconfig.json`要领

上面的`tsconfig.json`文件已经包含了使用 TypeScript 时必不可少的编译器选项。让我们一个一个地过一遍要点。

### TS/JS 互通

还可以设置 TypeScript 编译器来对 JS 文件和 TS 文件进行类型检查和编译。`allowJs`允许编译常规的 JavaScript 文件。如果您想在 JavaScript 文件中启用类型检查，您也可以启用`checkJs`。如果你刚刚开始，建议禁用`checkJs`，手动启用逐文件类型检查。为此，在您想要进行类型检查的 JS 文件的顶部添加一个`// @ts-check`注释。

另一个需要注意的编译器选项是`esModuleInterop`。这允许您使用 CommonJS 模块进行默认导入(例如`import React from 'react';`)。对于 TS 老兵来说，这个选项类似于`allowSyntheticDefaultImports`。唯一的区别是，它在编译期间添加了一些助手，以提高 Babel 的互操作性。

### 库和编译目标

有三个选项定义编译器如何解释您的 TS 代码。

`lib`概述用于编译的 TS 库文件。一些常用的库包括:

*   `esnext` -现代化的 ESnext 功能(直到第 3 阶段的建议)
*   `es201x` -每年的 ES 规格。请注意，包括一年将包括之前的所有年度规格(例如，`es2018`还将包括`es2017`、`es2016`和`es2015`)。
*   特定于 DOM 的 API。
*   `webworker` -面向[网络工作者](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)的 API。

`target`定义专家系统的目标版本。

`module`定义 TS 编译器将生成的模块类型。如果将`target`设置为`es5`或更低，它将默认为`commonjs`(node . js 兼容性的标准 CommonJS 模块)。否则，将默认为`esnext` (ES 模块)。

* * *

这部分到此为止。在第 2 部分中，我们将介绍如何通过逐渐添加类型来使您的 TypeScript 迁移变得轻松。我们还将了解 TypeScript 的类型系统的特点，以及改变您编写 TypeScript 应用程序的思维方式。

我再次强烈推荐巴萨拉特的[打字稿深度潜水](https://basarat.gitbooks.io/typescript/)这本书。他关于打字稿的书对我学习这门神奇的语言帮助很大。万一你被卡住了，[react flux](https://www.reactiflux.com/)Discord 服务器上的`#typescript`频道有一群可爱的人，他们对打字稿了如指掌。欢迎随时加入并提出问题！