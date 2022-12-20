# 已解决:树莓 Pi 权限

> 原文：<https://dev.to/katieadamsdev/raspberry-pi-index-html-permissions-4d35>

大家好！

我正在为一个树莓派项目寻求帮助。

是运行 Raspbian 和 Apache 服务器的 Raspberry Pi 3 型号 B+与 Apache 相关的一切都已启动并运行——例如，我可以在 Geany 中使用 gksudo 命令查看 generated e index.html 文件并编辑它。

然而，我的下一步是在/var/www/html 文件夹中设置 web 应用程序的其余部分。由于权限问题，我似乎不能这样做。该文件夹归 root 所有，并且有一组 root。

我已经搜索了网络和 StackOverflow，但是似乎也找不到任何解决方案:
A)对于安全性来说是确定的最佳实践
B)对于像我这样的 Raspbian 新手来说有意义
C)有效

我决定在这里提出我的问题。授予自己对/var/www/html 文件夹的编辑权限的安全方法是什么，它不会违反安全规则？如果你能提供任何关于这个命令正在做什么的解释，我将不胜感激。我想知道发生了什么事，这样我才能从中吸取教训。无红利(ex-dividend)