# 快速提示:以下是如何从你的 Mac 上卸载 Postgres

> 原文：<https://dev.to/irreverentmike/quick-tip-heres-how-to-uninstall-postgres-from-your-mac-1f98>

# 问题

今晚早些时候，我的 Macbook Pro 决定不再有磁盘空间，在时间机器备份过程中，它开始啃噬我剩下的最后一点可用空间。太好了。

在一片恐慌和卸载的狂潮中，我遇到了一个我已经很久没用过的 [Postgres](https://www.postgresql.org/) 9.x 安装程序。我想小心翼翼地卸载它，部分原因是我知道 Postgres 的安装过程会给你的 mac 带来大量不容易清理的东西，包括给你的设备添加一个用户帐户、一个应用程序和一系列设置。

天啊，多棒的旅程。

# 解

据我所知，没有一个简单的卸载程序适用于所有情况。最终，网络搜索之神将我带到了 GitHub 上的[这个美丽的要点](https://gist.github.com/Atlas7/b1a40a2ffd85728b33e7)，它列出了我需要做的一切来安全地完成卸载。(我还[分叉](https://gist.github.com/mbifulco/1e44a2e9b7e3bd784f01ef5a138da829)了要点，以防原件消失😅。)

我不想从这项工作中获得任何荣誉——它似乎最初来自于[这篇 2013 年的博文](http://hzchirs-blog.logdown.com/posts/142678-how-completely-uninstall-postgresql-9x-on-mac-osx)，它不是由我的 Bing-fu
回复的(是的，你没看错)。

我把它贴在这里，希望能帮助将来经历这种经历的其他灵魂。祝你好运！