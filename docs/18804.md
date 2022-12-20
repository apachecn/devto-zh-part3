# 快速生成漂亮的目录结构

> 原文：<https://dev.to/arximughal/generate-beautiful-directory-structures-quickly-50bi>

[![File Structure](img/6da821dc5a69e99ace4fa64f2b6bc31c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtDewFqB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3vih30wz26z07xeal71.png)

昨天我试图为我的一个项目的自述文件创建一个文件结构。我最终创造的这个还不错，但也不够好。因此，我开始寻找一种工具，它可以帮助我快速创建一个漂亮的目录结构。

在微软视窗系统中，`tree`命令做得相当好。但是我们在 linux 中没有它(或者至少在我写这篇文章的时候在 Ubuntu 18.04 中没有)。但是我设法找到了一个巧妙的解决方案:【Linux 的 Tree 命令。

## 如何安装树？

*   从[这里](http://mama.indstate.edu/users/ice/tree/)下载【Linux 的树命令
*   解压缩下载的文件(`unzip tree-1.8.0.tgz`)
*   `cd`放入解压后的文件夹
*   执行`make`，然后执行`make install`

就是这样。您已经在终端中安装并使用了 tree 命令。

## 基本用法

打开终端并执行

```
tree /path/to/directory 
```

*有关详细的文档和帮助，请查看 Tree 1 . 8 . 0T3 的[在线手册页](http://mama.indstate.edu/users/ice/tree/tree.1.html)*

在这里，您已经在终端中创建了一个树形结构。

选择树结构，复制它，并在任何你想要的地方使用它😊