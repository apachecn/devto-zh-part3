# 如果 Debian 说“下面的包被保留了”

> 原文：<https://dev.to/wincentbalin/if-debian-says-the-following-packages-have-been-kept-back-1dji>

根据[这个 askubuntu.com 问题](https://askubuntu.com/questions/601/the-following-packages-have-been-kept-back-why-and-how-do-i-solve-it)，如果你的基于 Debian 的 Linux 在升级时告诉你`The following packages have been kept back:`，你可能会这样做:

1.  运行`sudo apt-get --with-new-pkgs upgrade`
2.  运行`sudo apt-get install <list of packages kept back>`
3.  (如果没有别的帮助，运行`sudo apt-get dist-upgrade`，但是要小心包的自动移除！)