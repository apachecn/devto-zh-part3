# 有用的 Azure 工具列表

> 原文：<https://dev.to/melezhik/a-list-of-useful-azure-tools-1i0d>

最近开始玩 Azure/Powershell 脚本。这里有一组现成可用的工具:

[https://sparrowhub.org/search?category=azure](https://sparrowhub.org/search?category=azure)

所有的脚本都是通过 [Sparrow](https://metacpan.org/pod/Sparrow) 发布的——用 Perl 编写的脚本管理平台。

您可以在 Windows/Linux 平台上使用这些脚本。

每个脚本都有统一的安装和运行方式，只需`$ sparrow plg install $plugin-name`安装，`$ sparrow plg run $plugin-name --param key=value --param key2=value2`运行即可。

您可以通过创建任务来记忆脚本配置:

`$ sparrow project create azure-scripts`
`$ sparrow task add azure-scripts $task-name $plugin-name`
`$ sparrow task ini azure-scripts $task-name $plugin-name`

我将很快添加更多的脚本。

* * *

感谢您的阅读，并告诉我这些脚本是否有用。