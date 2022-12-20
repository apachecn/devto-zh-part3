# 不用换行符用 Python 打印

> 原文：<https://dev.to/wincentbalin/print-in-python-without-newline-17i7>

如果你想用 Python 打印一个末尾不带换行符的字符串，就像 [this](https://stackoverflow.com/questions/493386/how-to-print-without-newline-or-space) :
这样做

```
# ... and if you are in Python 2: from __future__ import print_function print('.', end='') 
```

Enter fullscreen mode Exit fullscreen mode