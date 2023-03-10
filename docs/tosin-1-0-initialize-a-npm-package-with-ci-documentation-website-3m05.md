# Tosin 1.0:用 CI 初始化 npm 包，文档网站...

> 原文：<https://dev.to/ffloriel/tosin-1-0-initialize-a-npm-package-with-ci-documentation-website-3m05>

Tosin 是开始创建 npm 包的一种固执的方式。从 CI 到文档网站，它应有尽有。下面是它的功能列表。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)全人类 / [尽管](https://github.com/FullHuman/tosin)

### 初始化一个包含所有内容的 npm 包，从 CI 到文档网站

<article class="markdown-body entry-content container-lg" itemprop="text">

# 当然了

[![Build Status](img/24b1a0f22327636ee0e645edbaf11003.png)](https://raw.githubusercontent.com/FullHuman/tosin/master/)[![CircleCi](img/9dc134c7f718aadacc4473d4c4da6490.png)](https://raw.githubusercontent.com/FullHuman/tosin/master/)[![dependencies Status](img/0e2a2f3286dc083ae76d8d4b4a78a233.png)](https://david-dm.org/fullhuman/tosin)[![devDependencies Status](img/5b7bd0fa1a615a643de8a49f36a71e10.png)](https://david-dm.org/fullhuman/tosin?type=dev)[![Codacy Badge](img/be70436e225b698ec884c6fbbbaec7e9.png)](https://www.codacy.com/app/florielfedry/tosin?utm_source=github.com&utm_medium=referral&utm_content=FullHuman/tosin&utm_campaign=Badge_Grade)[![Codacy Badge](img/a12b34dba3f7e74b153fa1d778294e96.png)](https://www.codacy.com/app/florielfedry/tosin?utm_source=github.com&utm_medium=referral&utm_content=FullHuman/tosin&utm_campaign=Badge_Coverage)[![styled with prettier](img/283916b730a6272e7a62f674e68cb747.png)](https://github.com/prettier/prettier)[![npm](img/1651e97631833586d24e5190daba8ed9.png)](https://www.npmjs.com/package/tosin)[![license](img/7513bb580e4122be39d8035ee43cb9db.png)](https://raw.githubusercontent.com/FullHuman/tosin/master/)[![dependabot](img/c749f04b58cf5e83bc62b1ec9b6451eb.png)](https://raw.githubusercontent.com/FullHuman/tosin/master/)

[![Tosin logo](img/d82693ceb6f0350cfb841153d80b9cda.png)](https://camo.githubusercontent.com/7e9ba949f68bd53b3cad060d718058e32cdf0e6c/68747470733a2f2f696d6775722e636f6d2f6c566143586b552e706e67)

## 托辛是什么？

Tosin 是开始创建 npm 包的一种固执的方式。从 CI 到文档网站，它应有尽有。下面是它的功能列表。

### 连续累计

持续集成有助于关注您的存储库的状态。构建分支并通过所有的测试，你当然可以手动完成所有的工作，但是对于外部贡献者和拉请求，你需要一种自动化过程的方法。

Tosin 为 circleci 和 Travis-ci 分别提供了一个简单的配置。

*   CircleCi 将使用 npm install、lint 构建包，以确保没有容易出错的代码，并运行测试。看一看关于如何在主分支上添加代码覆盖的`Next Steps`部分。
*   Travis configuration 只会用 Nodejs 的另一个版本测试你的包…

</article>

[View on GitHub](https://github.com/FullHuman/tosin)

### 持续整合

持续集成有助于关注您的存储库的状态。您的分支是否构建并通过了所有的测试？当然，您可以手动完成所有这些工作，但是对于外部参与者和拉式请求，您需要一种自动化流程的方式。

Tosin 为 circleci 和 Travis-ci 分别提供了一个简单的配置。

*   CircleCi 将使用 npm install，lint 构建包，以确保没有容易出错的代码，并运行测试。看看[文档网站](https://fullhuman.github.io/tosin/Guide.html#next-steps)上的`Next Steps`部分，了解如何在主分支上添加代码覆盖率。
*   Travis configuration 将只使用 Nodejs 的另一个版本测试您的包。你可以把它看作是一个备份，以防 CircleCI 出现问题，阻止它构建你的包。通过查看 Travis 状态，您还可以很容易地了解您的构建是否没有通过测试。由于 CircleCI 做得更多，即使您的测试成功通过，它也可能失败。

### 棉绒+漂亮的

Eslint 用于林挺。

> ESLint 是一个用于识别和报告 ECMAScript/JavaScript 代码中发现的模式的工具，其目标是使代码更加一致并避免 bug。

[更漂亮的](https://prettier.io/)用来格式化代码。

> 漂亮是一个固执己见的代码格式化程序。它通过解析您的代码并根据自己的规则(考虑到最大行长度，必要时换行)重新打印代码来强制执行一致的风格。

### 测试框架笑话

Jest 是最流行的 Javascript 测试框架之一。与其他测试框架相反，Jest 在其框架中包含了断言库和嘲讽。

> Jest 是一个 JavaScript 测试框架，旨在确保任何 JavaScript 代码库的正确性。它允许你用一个平易近人的、熟悉的、功能丰富的 API 来编写测试，并快速给出结果。

### GitHub 文件社区

准备好接受来自社区的帮助和问题。包括一个标准的拉式请求模板和三个问题模板:

*   错误报告模板
*   功能请求模板
*   其他一切的标准问题模板

一份[行为准则](https://www.contributor-covenant.org/)的存在是为了确保每个人都感到受欢迎。

### 用 Rollup 构建系统

[Rollup](https://rollupjs.org/guide/en/) 用来编译你的代码。您可以使用 es 模块并以 ES 和 commonjs 格式编译您的代码。

> Rollup 是 JavaScript 的一个模块捆绑器，它将小段代码编译成更大更复杂的东西，比如一个库或应用程序。它为 JavaScript 的 ES6 修订版中包含的代码模块使用新的标准化格式，而不是以前的特殊解决方案，如 CommonJS 和 AMD。ES 模块让你自由无缝地组合你最喜欢的库中最有用的单个函数。这最终在任何地方都是可能的，但是 Rollup 让你今天就可以做到。

### 准备自动完成

当有人安装你的软件包时，你想提供自动补全功能吗？如果你这样做了，`types/index.d.ts`就可以完成了。
它在`package.json`中被设置为包含你的类型的文件。大多数 IDE 和代码编辑器将使用该文件来提供自动完成功能。
这是改善开发者体验的好方法。

### 文档网站准备部署

GitHub 提供了在很长一段时间内每个存储库托管一个网站的可能性。您可以利用这一点为您的包创建一个文档网站，并将它直接托管在 GitHub 上。

Tosin 用 [vuepress](https://vuepress.vuejs.org/) 和一个将网站部署到 GitHub 页面的脚本来初始化您的项目。该脚本会将文件部署到存储库的 gh-pages 分支。