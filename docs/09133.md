# 如何通过内容找到一个文件？

> 原文：<https://dev.to/rdeavila/how-to-find-a-file-by-content-4759>

这个技巧在 Linux 和 macOS 上都有效。

```
grep -lr "my text" *.txt 
```

Enter fullscreen mode Exit fullscreen mode

该命令:

*   列出模式为`my text`的文件
*   递归地
*   在所有`*.txt`文件中