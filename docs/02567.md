# Linux 中的 inode 是什么？

> 原文：<https://dev.to/coderoo7/what-is-inode-in-linux-4931>

我正在努力理解 inode 是什么的概念？在阅读了这篇[文章](https://mobile.linuxtoday.com/blog/what-is-an-inode.html)之后，我了解到 inode 是一种数组，其中 inode-number of file 是该数组的索引‘I’。
我们知道数组有静态大小，这就是为什么像 ext3 这样的老文件系统有固定数量的索引节点..意味着你只能在 ext3 中创建有限数量的文件。

我可以限制在 Linux 现代文件系统如 ext4 中创建的文件数量吗...？