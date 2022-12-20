# OpenBSD 6.4 上的时区管理

> 原文：<https://dev.to/nabbisen/managing-timezone-in-openbsd-4p95>

**封面图片由 [Tentes](https://pixabay.com/en/users/Tentes-682637) 原创，并经过精心编辑。*

# 总结

这篇文章是关于如何在 [OpenBSD](https://www.openbsd.org/) 中检查和配置时区的。

### 环境

*   操作系统:OpenBSD 6.4 amd64

<center>✿ ✿ ✿</center>

# 教程

### 第一步:检查时区

让我们用 [`date`](https://man.openbsd.org/date) 命令:
检查当前时区

```
$  date
Mon Dec  3 13:43:30 UTC 2018 
```

Enter fullscreen mode Exit fullscreen mode

我们有 UTC。

根据他们关于 [TZSET(3)](https://man.openbsd.org/tzset.3) 的文档，

*   `/etc/localtime`是“本地时区文件”。
*   `/usr/share/zoneinfo`是“时区信息目录”。

然后，让我们通过检查`/etc/localtime` :
来调查为什么会这样

```
$  ls -l /etc/localtime
lrwxr-xr-x  1 root  wheel  23 Jun 12 21:27 /etc/localtime ->  /usr/share/zoneinfo/UTC 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道这是因为`/etc/localtime`与`/usr/share/zoneinfo/`中的`UTC`相关联。

### 第二步:配置时区

我们如何配置时区？
让我们用 [`ln`](https://man.openbsd.org/ln) 命令改变链接:

```
#  ln -fs /usr/share/zoneinfo/%your-location% /etc/localtime 
```

Enter fullscreen mode Exit fullscreen mode

以我在日本的情况:

```
#  ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
$  date
Mon Dec  3 22:56:30 JST 2018 
```

Enter fullscreen mode Exit fullscreen mode

是😃

* * *

# 引用

[OpenBSD 常见问题-一般问题-时区](https://www.openbsd.org/faq/faq8.html#TimeZone)

他们说:

> 如果您需要更改时区，您可以在/usr/share/zoneinfo 中创建一个新的指向相应时区文件的符号链接。

<center>✿ ✿ ✿</center>

为🕊服务愉快