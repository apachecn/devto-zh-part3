# fzf 是我 2018 年的工具发现

> 原文：<https://dev.to/oliverburn/fzf-is-my-tool-discovery-of-2018-4gb6>

今年我在试验 Vim 插件时偶然发现了命令行工具 [fzf](https://github.com/junegunn/fzf) (说来话长)。

用网站的话说:

> 这是一个交互式的 Unix 命令行过滤器，可用于任何列表；文件、命令历史、进程、主机名、书签、git 提交等。

它很快成为我在 Linux 和 macOS 上使用的不可或缺的工具。我如何使用它的例子有:

*   搜索我的命令历史记录。

*   与 [z](https://github.com/junegunn/fzf/wiki/examples#z) 相结合，它使得浏览以前的目录变得很简单。

*   在运行[分类账](https://www.ledger-cli.org/)之前，我使用下面的脚本从文件`accounts.ledger`中选择一个账户。

```
#!/bin/bash
account=$(sed 's/^account //' accounts.ledger | fzf)
if [[ $account != "" ]]; then ledger reg $account "${@}"
fi 
```