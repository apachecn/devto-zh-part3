# 如何使用 windows 命令提示符删除空文件夹

> 原文：<https://dev.to/adzubla/how-to-delete-empty-folders-using-windows-command-prompt-1jig>

打开目标文件夹所在的命令提示符，运行:

```
ROBOCOPY target target /S /MOVE 
```