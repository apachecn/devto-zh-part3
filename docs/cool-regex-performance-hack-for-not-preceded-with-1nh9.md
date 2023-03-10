# 酷正则表达式性能黑客为“之前没有”

> 原文：<https://dev.to/alexjitbit/cool-regex-performance-hack-for-not-preceded-with-1nh9>

这篇文章的第一部分只适用于运行 ASP.NET 的用户，如果不感兴趣，你可以直接[跳到 regex hack](#optimizing-regex) ，但是我强烈建议你阅读整个故事。

## 调试 ASP.NET CPU 峰值

大约三周前，我们开始在应用程序的生产服务器上看到不寻常的 CPU 负载峰值。我们确实有一个[超级有用的工具](https://github.com/jitbit/cpu-analyzer)(最初由[山姆·萨弗伦](https://samsaffron.com/)编写，由我们分叉)显示使用所有 CPU 时钟的确切调用堆栈

但是问题是，您必须在 CPU 负载达到峰值的时候将这个工具“附加”到进程上。“捕捉”那一刻可以是...夏威夷大学（TheUniversityofHawaiiatManoa）...很难。

尽管我们已经设置了 AWS，当 CPU 负载超过 75%并持续 5 分钟(顺便说一下，这是任何运行业务关键型 SaaS 应用程序的人都必须具备的)时，它会向我们发送短信，告诉我们运行到我的笔记本电脑、远程登录到服务器、进入命令行、连接到过载的进程等所需的时间...CPU 负载恢复正常。可恶。

幸运的是，我的合伙人写了一个简短的窗口。BAT 脚本可以自动做到这一点——如果 CPU 负载高于 75%,该脚本会找到 web 应用程序进程 ID，启动该工具，将其附加到 PID，并将调用堆栈数据转储到一个文本文件中(如果您要使用该脚本，请将“OurApplicationPool”更改为运行您的应用程序池的实际用户帐户)。

放心使用:

```
@Echo OFF

SET /A "MAXUSAGE=75"

For /F %%P in ('wmic cpu get loadpercentage ^| FINDSTR "[0-9]"') do (
echo %%P
    IF %%P GTR %MAXUSAGE% (
        for /f "tokens=1,2 delims= " %%A in ('tasklist /FI "USERNAME eq OurApplicationPool" /fo table /nh') do cpu-analyzer.exe %%B >> log_%date:~-4,4%%date:~-7,2%%date:~-10,2%.txt
    )
) 
```

然后，我们简单地在 Windows 任务调度程序中设置了一个每 5 分钟运行一次的作业(我知道这很难看，但我们需要一个快速而又肮脏的解决方案)。

## 优化正则表达式

在对日志和调用栈进行了一番挖掘之后，发现大部分 CPU 时间都花在了`Regex.Search`上。我们的应用程序在后端和前端都使用了大量的正则表达式。比如将 markdown 和“BbCode”(用于内部存储)转换为“普通”HTML(用于 UI 和邮件)时。

长话短说，在数小时盲目尝试不同的正则表达式模式、测试和基准测试后，我发现:

如果你想匹配一条规则，即“某物前面没有‘A’”，不要使用这样的代码:`(^|[^a])something`。

第一组基本上是说“它要么是文本的开头，要么不是字母 A”，大多数人觉得比“regex lookback”更易读，包括我自己。但是结果是 regex 的“lookbehinds”和“lookaheads”比涉及“字符串开始/结束”的模式快 3-4 倍，尤其是在处理大的字符串时。

在我们的例子中，用一个`(?<!a)`(字面意思是“前面没有”)替换`(^|[^a])`，让我们的代码快了 3 倍。我写了一个快速的基准测试，在两者中都进行了 10000 次搜索替换迭代。NET 和 JavaScript regex 引擎(我们两个都用)，结果是:

`(^|[^a])something`-1346 毫秒(平均)
`(?&lt;!a)something`-389 毫秒(平均)

哇，快了三倍多！显然，输入文本越长，影响越大。

酷毙了。但是我们能进一步改进它吗？有没有办法让“前面没有 A”的规则变得更快？

原来是有的！不幸的是，网上没有太多的正则表达式性能分析，所以(再次)我在这里独自一人...但幸运的是，我记得几年前读过一篇很酷的 HackerNews 帖子，该帖子提出了一种优化规则的好方法，比如“not befored with”或“not ends with”或“not ends with”等。基本上诀窍是这样的:

`asomething|(something)`

不错吧。如果你想匹配“前面没有 A 的东西”，只需匹配你不想要的东西，然后忽略全部匹配，只看“组 1”！基准:

`(?<!a)something`-389 毫秒(平均)
`asomething|(something)`-16 毫秒(平均)

神圣的嘘！这几乎比我的原始代码快一百倍！