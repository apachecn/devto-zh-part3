# GitHub 操作入门？尝试操作工具包

> 原文：<https://dev.to/mscccc/getting-started-with-github-actions-try-actions-toolkit-1902>

如果您正在使用 Node.js 编写自己的动作，请节省一些时间，看看 Actions Toolkit。Jason 正在收集常见的任务，并将它们提取到一个模块中，您可以使用它来构建您的行动。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [贾西内科](https://github.com/JasonEtco) / [动作-工具包](https://github.com/JasonEtco/actions-toolkit)

### 🛠在 Node.js 中构建 GitHub 动作的工具包

<article class="markdown-body entry-content container-lg" itemprop="text">

### GitHub 操作工具包

在 Node.js 中构建 GitHub 动作的固执己见的工具包
[用法](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#usage)[API](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#api)[如何测试你的动作](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#how-to-test-your-github-actions)[常见问题](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#faq)

[![GitHub Actions status](img/6d223ba496f9cb4a9e196ab0ec5bf5e1.png) ](https://github.com/JasonEtco/actions-toolkit) [ ![Codecov](img/7a268eb2e219dba56fd66f4ce53ba860.png)](https://codecov.io/gh/JasonEtco/actions-toolkit/)

这个工具包是官方工具包的自以为是的替代品(和包装)。为了提高性能，使许多特性成为可选的，所以你可能更喜欢使用它而不是这个库。

## 使用

### 装置

```
$ npm install actions-toolkit
```

```
const { Toolkit } = require('actions-toolkit')
const tools = new Toolkit()
```

### 引导一个新动作

```
$ npx actions-toolkit my-cool-action 
```

这将创建一个包含以下文件的新文件夹`my-cool-action`:

```
├── Dockerfile
├── action.yml
├── index.js
├── index.test.js
└── package.json 
```

## 应用程序接口

*   [工具箱类](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolkit-options)
*   [经过认证的 GitHub API 客户端](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsgithub)
*   [测井](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolslog)
*   [获取工作流程的输入](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsinputs)
*   [从您的操作中输出信息](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsoutputs)
*   [斜线命令](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolscommandcommand-args-match--promise)
*   [读取文件](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsreadfilepath-encoding--utf8)
*   [运行 CLI 命令](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsexec)
*   [结束动作的过程](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolsexit)
*   [检查 webhook 事件负载](https://raw.githubusercontent.com/JasonEtco/actions-toolkit/main/#toolscontext)

### 工具包选项

#### 事件(可选)

可选列表…

</article>

[View on GitHub](https://github.com/JasonEtco/actions-toolkit)