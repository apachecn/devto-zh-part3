# JSX 组件概述和“jsx-info”的正确用法

> 原文：<https://dev.to/embiem/overview-of-jsx-components-and-prop-usage-with-jsx-info-3pm9>

[https://www.youtube.com/embed/e_vtfYJW9aM](https://www.youtube.com/embed/e_vtfYJW9aM)

Brian Mock 制作的非常酷的工具，它展示了你的组件道具的概述以及它们在代码库中的用法:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [【波形】](https://github.com/wavebeem)/[【jsx 信息】](https://github.com/wavebeem/jsx-info)

### 显示 JSX 组件和正确使用的报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# jsx 信息

显示 JSX 组件和正确使用的报告。

观看我的[演示](https://youtu.be/e_vtfYJW9aM)视频了解更多信息。

## 为什么

首先，我认为看到这些信息会很酷。但更重要的是，我认为`jsx-info`可以用来帮助重构你的代码。

假设你有一个名为`<DataTable>`的组件，它需要 43 个不同的道具。如果你需要从头开始重写`<DataTable>`，你可能不想保留那么多不同的道具。使用`jsx-info`,你可以分析哪些道具用得最多，并首先开始移植那些功能。

如果某个特定属性的使用率非常低，您甚至可以选择去掉该属性，并重写调用代码来使用其他属性。

此处的预期工作流程是运行`jsx-info`并将数据与您的 prop-types 或 TypeScript/Flow 类型定义进行比较，以找出差异。

## 装置

自动安装并…

</article>

[View on GitHub](https://github.com/wavebeem/jsx-info)

> 假设你有一个名为`<DataTable>`的组件，它需要 43 个不同的道具。如果你需要从头开始重写`<DataTable>`，你可能不想保留那么多不同的道具。使用 jsx-info，您可以分析哪些 props 使用得最多，并首先开始移植该功能。