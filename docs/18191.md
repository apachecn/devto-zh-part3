# 用 Scala 和 shapeless 解析 CSV

> 原文：<https://dev.to/devkat/csv-parsing-with-scala-and-shapeless-5hho>

#### [无形的](https://github.com/milessabin/shapeless)库为构建通用的、可重用的组件提供了极好的基础。我们演示使用类型`HList`和`Generic`将字符串解析成 case 类。

### 简介

这篇文章是对即将发表的文章[用 Akka 流进行实时日志处理](https://www.becompany.ch/en/blog/2016/07/29/realtime-log-processing)的补充，它涉及到处理来自 web 服务器日志的日志条目。为了方便和清晰起见，我们希望将日志文件行字符串解析成 case 类实例，允许我们以语义方式处理它们。

有无数的库可用于解析字符串；我们将使用 [scala-csv](https://github.com/tototoshi/scala-csv) 和 [shapeless](https://github.com/milessabin/shapeless) 来展示一种通用的可扩展方法。我们使用的一些代码基于不成形代码库中的 [CSV 示例](https://github.com/milessabin/shapeless/blob/master/examples/src/main/scala/shapeless/examples/csv.scala)。

### 获取来源

示例项目的源代码是 github 上的[。](https://github.com/becompany/akka-streams-example)

### 日志格式

我们的示例使用包含以下组件的 web 服务器日志格式:

1.  远程 IP 地址
2.  以毫秒为单位的时间(Unix 时间戳)
3.  请求路径
4.  用户代理人

这大致对应于以下 nginx 日志配置:

```
log\_format my\_log\_format
  '"$remote\_addr","$msec","$request","$http\_user\_agent"'; 
```

Enter fullscreen mode Exit fullscreen mode

日志条目如下所示:

```
"1.2.3.4","1466585706027","/foo","Chrome" 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用下面的 case 类来表示日志事件: