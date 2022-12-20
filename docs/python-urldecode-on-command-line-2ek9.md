# 命令行上的 Python urldecode

> 原文：<https://dev.to/k4ml/python-urldecode-on-command-line-2ek9>

我有一些包含编码字符的 url 的日志，我需要从 URL 中提取一些数据。四处搜索，我找到了这个 [stackexchange 的答案](https://unix.stackexchange.com/questions/159253/decoding-url-encoding-percent-encoding) :-

```
alias urldecode='python3 -c "import sys, urllib.parse as ul;print(ul.unquote_plus(sys.argv[1]))"' 
```

Enter fullscreen mode Exit fullscreen mode

那么你可以把它当作:-

```
urldecode '%22status%22%3A%22SUCCESS%22'
"status":"SUCCESS" 
```

Enter fullscreen mode Exit fullscreen mode

现在那是完美的。但是我要解码的数据必须通过 unix 管道传递，就像这样:-

```
grep 'status: 504' /var/log/local0* | awk '{ print $10}' | xargs urldecode 
```

Enter fullscreen mode Exit fullscreen mode

但是这是行不通的，因为 xargs 看不到别名。所以我们也给 xargs 起了别名:-

```
alias xargs='xargs ' 
```

Enter fullscreen mode Exit fullscreen mode

但是这仅适用于第一行。如何让 xargs 对每一行执行？

```
alias xargs='xargs -L 1 ' 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以对从 grep 中导出的每一行运行 urldecode 了！