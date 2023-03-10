# 林挺你的反应+打字项目与 ESLint 和更漂亮！

> 原文：<https://dev.to/dorshinar/linting-your-reacttypescript-project-with-eslint-and-prettier-8hb>

最近我们在工作中开始了一个新项目，用 React + Typescript 编写。当然，像任何其他项目一样，我们希望它被自动地进行 linted 和样式检查，所以我们自己不必担心这个问题。

林挺打字稿的两个最大、最熟悉的工具是 [TSLint](https://palantir.github.io/tslint/) 和 [ESLint](https://eslint.org/) 。虽然 ESLint 存在了很长一段时间，但它从来没有对 Typescript 提供合适的、生产就绪的支持，所以大多数 Typescript 项目都是用 TSLint 编写的。

我的任务是为我们的项目找到合适的工具。这是一个全新的项目，因此没有必要进行代码迁移或规则迁移。首先我看了 TSLint。它对 TypeScript 有很好的支持，加上一个优秀的 [VSCode 扩展](https://marketplace.visualstudio.com/items?itemName=eg2.tslint)。这看起来绝对是个正确的选择，直到我偶然发现了[的这篇](https://eslint.org/blog/2019/01/future-typescript-eslint)文章。我发现它很有趣，但如果你不想读，我会在这里给你要点。

在微软 2019 年前 6 个月的 [TypeScript 路线图](https://github.com/Microsoft/TypeScript/issues/29288)上，他们提供了一整节关于 TypeScript 开发者对适当 ESLint 支持的日益增长的需求。此外，他们谈论 ESLint 的架构如何更适合他们的需求，最后他们说他们打算完全接受 ESLint 作为 Typescript 项目的林挺工具，并保证改进 ESLint 的 Typescript 支持，以努力匹配 TSLint 的支持。

本文的其余部分将讨论一个新的[存储库](https://github.com/typescript-eslint/typescript-eslint)，它旨在包含 ESLint 能够解析和 Lint 类型脚本代码所需的核心项目。

经过进一步的测试和比较，我相信 ESLint 实际上是这项工作的合适工具(不仅因为我信任 Typescript 的团队😉).

更新-5 月 10 日:
[创建 React App v3](https://github.com/facebook/create-react-app/releases/tag/v3.0.0) 也用@typescript/eslint 启动了林挺的 TypeScript 项目，所以我坚信我做出了正确的选择。

[![](img/b42d1f088aa418461d662de87f2a4f5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XGVSBXRn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2834/1%2AzpFPwbBffYcKQA_ovMFyWQ.png)

哇，这是一个很长的介绍。让我们开始编码吧！

首先，我们需要创建一个新项目。对于这篇文章，我将使用`create-react-app`，但是无论您选择哪个样板文件(或者自己创建的)都可以。

`npx create-react-app eslint-react-intro --typescript`

对于不熟悉的人来说，`npx`是和`node@5.2.0`一起首先推出的一个工具。简而言之，它允许我们轻松运行 npm 包的二进制文件，而无需全局安装。它实际上做的还不止这些，我们鼓励你去阅读[这篇](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)伟大的文章。

现在我们有了一个基本的 react 应用程序，但我们在这里是为了林挺，所以让我们安装一些依赖项:

`npm i -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin`

`eslint`是一个明显的依赖关系，但是什么是`@typescript-eslint/parser`和`@typescript-eslint/eslint-plugin`？

默认情况下，ESLint 依赖解析器来读取 Javascript 代码，并将其“翻译”成 ESLint 可以理解的语言(也称为`ESTree`)。默认情况下，ESLint 使用 [Espree](https://github.com/eslint/espree) ，这对于读取 JS 代码非常有用，但是无法读取 Typescript。`@typescript-eslint/parser`是一个替代的解析器，可以读取 Typescript 代码并生成所述 ESTree，所以我们可以告诉 ESLint 改用它。`@typescript-eslint/eslint-plugin`只是一个你可以打开或关闭的规则列表。

现在我们有了基本的依赖关系，让我们来配置 ESLint。

ESLint 有一个很好的互动工具，你可以运行它:

`eslint --init`

它会问你一系列问题来帮助你配置它。我更喜欢自己定义配置，所以我将创建配置文件— `.eslintrc.js` (ESLint 也支持 JSON 和 YAML)。包含以下内容: