# Codenarc IntelliJ 插件已更新

> 原文：<https://dev.to/erichelgeson/codenarc-intellij-plugin-updated-2fge>

[Codenarc](http://codenarc.sourceforge.net/) 是一款针对 Groovy 的静态代码分析工具。它已经存在了很长一段时间，但是它缺少的一个特性是能够看到 IntelliJ 内部的违规行为。

幸运的是，我的一位同事看到 Daniel Demus 最近更新了最初由 Cédric Champeau 创建的 IntelliJ 插件。

IntelliJ:
[![](img/689138676bfd675c8f89e2d1f0257616.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kKN51Qmu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lxj2vscqn3wujw0grfqj.png) 内部违反规则权限的示例

你可以在这里获得插件[并检查代码或者在](https://plugins.jetbrains.com/plugin/12106-codenarc-updated) [Github 项目](https://github.com/demus-nine/CodeNarc-Updated)上记录一个 bug

一个警告是，该插件目前不会自动导入您的`codenarc.groovy|xml`规则文件-您必须在检查设置对话框中手动启用规则。虽然有一个[特性请求](https://github.com/demus-nine/CodeNarc-Updated/issues/2)打开来做这件事。