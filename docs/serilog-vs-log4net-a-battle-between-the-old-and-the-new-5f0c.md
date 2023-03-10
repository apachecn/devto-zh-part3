# serilog vs log4net——新旧之战

> 原文：<https://dev.to/thomasardal/serilog-vs-log4net-a-battle-between-the-old-and-the-new-5f0c>

Serilog 和 log4net 是两个非常流行的日志消息的选择。NET 应用程序。在这篇文章中，我将向你展示如何配置和使用每个框架，并列出每个框架的优缺点。我甚至敢推荐您使用其中一个框架:)

让我们从每个框架的一些信息开始。

## seriol

Serilog 于 2013 年推出，是最新的日志框架之一。这绝对不是一件坏事，因为该框架使用了. NET 的一些更新更高级的特性。结构化日志可能是最引人注目的，但是丰富的概念也是使 Serilog 与许多其他日志框架相比独一无二的原因。

Serilog 在不断增长，每个月都会推出新的日志目标。塞里洛周围有一个很大的社区。与其他框架相比，参与的人真的很好，并且愿意帮忙。

## log4net

log4net 是团队中的老男孩。NET 日志框架。它最初是作为 log4j 的一个端口推出的，现在已经发展成为由 Apache Foundation 运行的一个独特的产品。您会发现很多现有的项目使用 log4net 来满足他们的日志需求。在 2017 年的最新版本中，主框架的开发在过去几年中几乎停滞不前。尽管新的 appenders 正在开发中，但是 log4net 实际上对现代 NoSQL 数据库也有相当不错的支持。

## 配置

log4net 和 Serilog 都需要配置才能开始日志记录。让我们深入每个框架，看看配置有多简单。

### seriol

Serilog 支持基于 XML 和 C#的配置。我从未听说过有人使用 XML 方法，所以这就是为什么本节的其余部分将展示用 C#编写的配置代码。能够用强类型语言配置 Serilog 是一个很大的好处。

为了配置简单的文件日志记录，我们编写这样的配置代码:

```
Log.Logger = new LoggerConfiguration()
    .WriteTo.File("log-.txt", rollingInterval: RollingInterval.Day)
    .CreateLogger(); 
```

`LoggerConfiguration`类是配置和创建 Serilog `ILogger`对象的关键部分。在 Serilog 中，日志目的地被称为接收器，并使用扩展方法添加到`WriteTo`属性中。在上面的例子中，我告诉 Serilog 将消息记录到一个滚动文件中。最后，我调用`CreateLogger`方法，并在`Log`类的静态`Logger`属性上设置输出。这确保了从您的程序中轻松访问`ILogger`对象。

### log4net

和 Serilog 一样，log4net 可以用 XML 和 C#来配置。C#方法显然是后来才加入的，并不是很直观(IMO)。因为你在网上找到的大多数例子都是 XML 格式的，所以我将在本文的其余例子中使用它。

log4net 配置通过`app.config`或专用的`log4net.config`文件:
添加

```
<log4net>
  <appender name="FileAppender" type="log4net.Appender.FileAppender">
    <file value="log-file.txt" />
    <appendToFile value="true" />
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%date %level %message%newline" />
    </layout>
  </appender>

  <root>
    <level value="ALL" />
    <appender-ref ref="FileAppender" />
  </root>
</log4net> 
```

注意文件 appender 如何拥有自己命名的`appender`元素，然后在`appender-ref`子元素的`root`元素中引用该元素。

为了使配置对程序的其余部分可见，在某处添加一点代码(如`AssemblyInfo.cs` ):

```
[assembly: log4net.Config.XmlConfigurator(Watch=false)] 
```

如前所述，配置并不是 log4net 的强项。幸运的是，有一些非常好的文档，并且因为它的悠久历史，在线有很多例子和博客帖子。

## 汇点和附加点

除非您想将所有内容都存储在/dev/zero 中，否则您需要某种方式来存储日志消息。上面的两个例子都展示了如何将日志消息存储到文件中。Serilog 有 Sinks 的概念，log4net 有 appenders。它们都涵盖了相同的需求，只是命名不同。

接收器和附加器可用于许多不同的目的地，如文件、数据库和远程服务。例如，elmah.io 为 Serilog 提供了一个接收器，也为 log4net 提供了一个 appender。它们都做同样的事情，将通过每个框架记录的所有消息存储到 elmah.io 中。每个实现的质量有很大差异，这就是为什么我建议您尝试每个接收器/附加器，看看它是否满足您的需求。

## 记录消息

如果不想记录任何消息，为什么要配置日志框架呢？让我们做点什么。

### seriol

登录 Serilog 非常简单:

```
Log.Information("Hello World"); 
```

还记得我们在上一节中如何初始化`Log`类吗？嗯，这里我们真的看到了好处。通过 Serilog，`Log`类具有所有可用日志级别的重载。在这个例子中，我记录了一条信息消息。

Serilog 的一个很大的好处就是结构化日志的特性:

```
Log.Information("Hello World from {FirstName}", "Thomas"); 
```

我告诉 Serilog 用`Thomas`代替`{FirstName}`，而不是使用普通的字符串连接。这产生了一个不错的日志消息，并告诉 Serilog 记住`FirstName`键及其值。这可以用来在 elmah.io 之类的服务或 MongoDB 或 Elasticsearch 之类的 NoSQL 数据库中记录结构化数据。

这并不是使用 Serilog 进行结构化日志记录的指南。如果你不知道这个概念，可以在 Serilog wiki 上查看一下[结构化数据](https://github.com/serilog/serilog/wiki/Structured-Data)。你将享受一顿大餐！

### log4net

通过 log4net 进行日志记录比 Serilog 复杂一点，但它仍然可以完成这项工作:

```
var log = LogManager.GetLogger(typeof(Bar));
log.Info("Hello World"); 
```

主要区别是需要`LogManager`。通常，每个类都有一个对日志记录器的引用，并重用同一个日志实例。

log4net 不支持结构化日志的概念。如 XML 配置中的`conversionPattern`元素所示，在写入存储时，您可以使用一些变量。但是在 Serilog 示例中包含类似于`FirstName`的属性是不可用的。

## 结论

我真的不喜欢选择 log4net，因为在过去的十年里，它一直是我的一个很好的(也是默认的)日志记录选择。但最后，Serilog 在大多数方面都更胜一筹。结构化日志记录、丰富(我甚至没有向您展示这些，但是查看 Serilog 文档中的例子)和基于 C#的配置都是赢家。

在大多数情况下，我不会建议您替换遗留系统上的日志框架，但是在这种情况下，我肯定会看到从 log4net 跳到 Serilog 的一些好处。

### seriol

*优势*

*   结构化日志记录和丰富
*   伟大的文档和社区
*   基于 C#的配置

*缺点*

*   要了解的更多功能

### log4net

*优势*

*   大量的文档和博客文章
*   当来自其他语言的日志框架时容易理解

*缺点*

*   无结构化日志记录
*   难以配置
*   项目似乎死了

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

这篇文章最初出现在 elmah.io 的博客上，时间是[https://blog.elmah.io/serilog-vs-log4net/](https://blog.elmah.io/serilog-vs-log4net/)