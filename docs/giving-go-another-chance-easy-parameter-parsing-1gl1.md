# 再给一次机会:简单的参数解析

> 原文：<https://dev.to/detunized/giving-go-another-chance-easy-parameter-parsing-1gl1>

从简单的开始是好的。我已经有了一个命令`klk in`，现在最好给它传递一些开关和参数。Cobra 有一个巨大的 [readme](https://github.com/spf13/cobra/blob/master/README.md) ，但是我发现查看他们自己的命令行工具源代码更容易获得例子。

```
var  inCmd  =  &cobra.Command{  Use:  "in [comment]",  Aliases:  []string{"i"},  Short:  "Clock in an entry",  Run:  func(cmd  *cobra.Command,  args  []string)  {  comment  :=  strings.Join(args,  " ")  timestamp  :=  time.Now()  fmt.Println("Adding an entry:",  comment,  "at",  timestamp.Format(time.UnixDate))  },  } 
```

我必须弄清楚如何连接字符串以及如何自定义日期格式。默认的日期格式有点奇怪。我猜它遵循 Go 路径(Go path？)开始了它的怪异之旅。谁希望日期默认打印成这样？什么是`m=+0.001801899`？

```
2019-01-23 12:04:12.760058 +0100 CET m=+0.001801899 
```

除此之外，一切都很容易。总的来说，Go 感觉像是 C 语言加上了一些 Python。问题是，你永远不知道什么时候你不得不从 Python 跳到 C，并且在这个过程中尽量不要撞到头。

现在让我们添加一个开关。我能想到的最简单的是`--quiet/-q`。Cobra 只有一句台词:

```
inCmd.Flags().BoolVarP(&quiet,  "quiet",  "q",  false,  "be quiet and don't print so much stuff") 
```

最终产品现在是这样工作的:

```
$ klk in some work
Adding an entry: some work at Wed Jan 23 12:16:20 CET 2019 
```

或者

```
$ klk in -q some work 
```

装运它！🚢

* * *

实现这一目标的谷歌搜索:

*   去连接字符串
*   现在开始吧
*   开始格式化日期

* * *

耗时:15 分钟
总耗时:5:15 小时