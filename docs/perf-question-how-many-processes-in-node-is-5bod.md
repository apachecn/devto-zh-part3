# Perf 问题:Node js 中有多少个进程？

> 原文：<https://dev.to/adam_cyclones/perf-question-how-many-processes-in-node-is-5bod>

我有一个 CMS 的这个想法(这只是一个技术练习)。传统的 CMS 具有模块/插件，这些模块/插件提供了扩展基本 CMS 功能的独立功能。

这让我想到，如果我在基于节点的原型中为每个插件生成一个进程会怎么样。我可以使用 sigstop 和 sigcont 按需暂停和恢复进程。事实上，我已经为此写了一个小的 API，它工作了！太棒了，但是如果(在我最疯狂的梦想中)我的 CMS 变成了下一个 WordPress 或者 Drupal 呢？我想一个用户可能会添加 100 个插件。普通的主机能处理所有这些吗？

有什么限制？

我可以假设一些插件依赖于另一个主插件，所以这些插件可以在同一个进程中运行，这将减少进程的数量。

你怎么想呢?

如果我正在寻找节点的节点性的基准，我会谷歌什么？