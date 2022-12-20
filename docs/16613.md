# 多语言构建工具？

> 原文：<https://dev.to/napicella/multi-language-build-tool--200h>

多语言构建工具的美妙之处在于它抽象出了特定语言的构建逻辑。因此，如果一个服务在后端使用 golang 和 Java，在前端使用 javascript，从事该项目的开发人员就不需要学习构建它的所有不同方法。这是一种使开发更快的标准化。

我一直在寻找开源多语言构建工具。
试过了[巴泽尔](https://bazel.build/)和[裤子](https://www.pantsbuild.org/index.html)，也简单看了一下[巴克](https://buckbuild.com/)。

对于单一回购来说，它们都工作得很好，但是当代码跨越不同的 git 回购时，它们就很难使用了。

你使用语言无关的构建工具吗？你用哪一个？在这种情况下，如何组织代码？

尼古拉