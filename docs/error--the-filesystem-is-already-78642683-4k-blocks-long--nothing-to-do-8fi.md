# 错误|文件系统已经有 78642683 (4k)块长。没事干！

> 原文：<https://dev.to/ankurjha21/error--the-filesystem-is-already-78642683-4k-blocks-long--nothing-to-do-8fi>

错误|文件系统已经有 78642683 (4k)块长。没事干！

如果在 linux for ext4 volume 中运行 resize2fs 命令时出现此错误，请运行 growpart 命令，然后运行 resize2fs 命令，如下所示:

sudo grow part/dev/xvda 1
sudo resize 2fs/dev/xvda 1