# 取消设置所有匹配模式的变量

> 原文：<https://dev.to/garettmd/unset-all-variables-matching-a-pattern-158d>

#### 取消设置所有以前缀开头的变量

当使用 Openstack CLI 时，您通常会得到一个 shell 脚本，您会`source`，它会将一组变量导出到您的会话中。我遇到过在两个不同版本的 Openstack 安装之间切换的问题，它们需要不同的变量。我遇到过这样的问题，当我试图使用另一个实例时，来自一个实例的变量仍然存在。我可以一个接一个地移除它们，但是我并不从事手工操作。相应地，下面的示例将取消设置所有 Openstack 变量(所有以`OS_`开头的变量)

为**狂欢**的是

```
# Bash
unset "${!OS_@}" 
```

Enter fullscreen mode Exit fullscreen mode

因为这是 bash，这是一个神秘的符咒，只有干瘪的老灰胡子才能解释。(那是讽刺)

对于 **zsh** 是

```
# zsh
unset -m "OS_*" 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在 zsh 中，这要简单得多。你使用`-m`标志(我相信它代表“匹配”)，然后使用全球匹配。我不确定它是否支持 regex，因为出于某种原因，zsh 内置的文档不容易找到。

显然，你可以用这个做很多事情。我倾向于在 CLI 工具(Openstack、AWS、Terraform 等)中大量使用它。希望对你有用！