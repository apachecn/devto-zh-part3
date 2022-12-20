# 报告指标使用。Net(核心)事件源和事件计数器

> 原文：<https://dev.to/expecho/reporting-metrics-using-net-core-eventsource-and-eventcounter-23dn>

几乎没有人质疑检测代码的重要性，并且在。Net 框架，更不用说无数伟大的第三方库，如 Nlog，Serilog 和 Log4Net。但是当涉及到报告指标(想一想:每分钟请求数、使用率、消息速率等等)时，内置的支持是非常有限的。当然，我们有性能计数器，但它们不能跨平台使用，因为它们仅适用于 Windows。幸运的是，有一种相对简单的方法可以使用 EventSource 类来报告指标，它是跨平台的，并且是框架的一部分，因此不需要外部依赖。

# 介绍事件计数器

ETW(Windows 的事件跟踪)和 [`EventSource`](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.7.2) 类早已成为。Net 框架。框架本身以及许多(第三方)库大量使用 EventSources。它允许您以最小的性能开销在应用程序代码中使用结构化日志记录。使用像 [PerfView](https://github.com/Microsoft/perfview) 这样的工具，可以在进程内或进程外收集数据。

在这篇博文中，我们将仔细看看不太知名的`EventCounter`类，它让我们使用`EventSource`来定义和报告指标。我特别想展示如何使用`EventListener`读取进程中的计数器，因为这不是很好的[文档](https://github.com/dotnet/corefx/issues/30988)。

首先，让我们看看 CoreFx 团队对 EventCounters 有什么看法(摘自他们的[教程](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)):

> 虽然 EventSource 很快，但是为非常频繁的事件记录太多事件仍然会影响性能。在本教程中，我们将介绍 EventCounter，这是一种为非常频繁的事件测量性能的机制。
> 
> 对于非常频繁发生的事件(例如，如果它每隔几毫秒发生一次)，一般来说，您会希望每个事件的性能开销非常低(例如，少于一毫秒)，否则它将花费大量的性能开销。记录一个事件，在一天结束时，需要向磁盘写入一些东西。如果磁盘不够快，您将丢失事件。除了记录事件本身，我们还需要一个解决方案。
> 
> 当处理大量事件时，知道每个事件的度量也不是很有用。大多数时候，我们需要的只是一些统计数据。因此，我们可以在流程本身中处理统计数据，然后偶尔写一个事件来报告统计数据，这就是 EventCounter 将为我们做的。

听起来很棒，不是吗？这是一个定义和报告指标的问题，框架将在内存中进行汇总和计算。我们需要做的就是指定数据输出的时间间隔。

# 菜谱

首先，我们需要定义一个包含`EventCounter`实例的`EventSource`。您可以在运行时动态创建`EventCounter`实例，但是由于我在这里记录的[的一个错误](https://github.com/dotnet/corefx/issues/30904)，您将在构造函数中创建至少一个`EventCounter`实例。

```
[EventSource(Name = "My-CustomMetricsEventSource-Minimal")]
public sealed class CustomMetricsEventSource : EventSource
{
    private EventCounter methodDurationCounter;

    private Dictionary<string, EventCounter> dynamicCounters =
        new Dictionary<string, EventCounter>();

    public static CustomMetricsEventSource Log = new CustomMetricsEventSource();

    public CustomMetricsEventSource()
    {
        methodDurationCounter = new EventCounter(nameof(methodDurationCounter), this);
    }

    public void ReportMethodDurationInMs(long milliseconds)
    {
        methodDurationCounter.WriteMetric(milliseconds);
    }

    public void ReportMetric(string name, float value)
    {
        if (!dynamicCounters.TryGetValue(name, out EventCounter counterInstance))
        {
            counterInstance = new EventCounter(name, this);
            dynamicCounters.Add(name, counterInstance);
        }
        counterInstance.WriteMetric(value);
    }
} 
```

我们将使用`CustomMetricsEventSource`在控制台应用程序中记录我们的指标。但是我们还需要一个监听器来拾取记录的度量并将它们发送到目的地。在这个例子中，我们将使用一个`EventListener` :
的自定义实现将数据写入控制台

```
internal class CustomMetricsEventListener : EventListener
{
    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        var counterData = eventData.ToEventCounterData();

        // Only write to console if actual data has been reported
        if (counterData?.Count == 0)
            return;

        Console.WriteLine(
            $"Counter {counterData.Name} reported values " +
            $"Min: {counterData.Min}, " +
            $"Max: {counterData.Max}, " +
            $"Count {counterData.Count}, " +
            $"Mean {counterData.Mean}, " +
            $"StandardDeviation: {counterData.StandardDeviation}, " +
            $"IntervalSec: {counterData.IntervalSec}");
    }
} 
```

上面听到的代码使用了`ToEventCounterData`方法。这其实是一种扩展方法。它返回一个`EventCounterData`实例，使得处理报告的度量数据更加容易:

```
public static class EventWrittenEventArgsExtensions
{
    public static bool IsEventCounter(this EventWrittenEventArgs eventData)
    {
        return eventData.EventName == "EventCounters";
    }

    public static EventCounterData ToEventCounterData(this EventWrittenEventArgs eventData)
    {
        if (!eventData.IsEventCounter())
            return null;

        return new EventCounterData(eventData);
    }
}

public class EventCounterData
{
    public EventCounterData(EventWrittenEventArgs eventData)
    {
        var payload = (IDictionary<string, object>) eventData.Payload[0];

        Name = payload["Name"].ToString();
        Mean = (float)payload["Mean"];
        StandardDeviation = (float)payload["StandardDeviation"];
        Count = (int)payload["Count"];
        IntervalSec = (float)payload["IntervalSec"];
        Min = (float)payload["Min"];
        Max = (float)payload["Max"];
    }

    public string Name { get; }
    public float Mean { get; }
    public float StandardDeviation { get; }
    public int Count { get; }
    public float IntervalSec { get; }
    public float Min { get; }
    public float Max { get; }
} 
```

最后，我们可以创建控制台应用程序，该应用程序将调用`CustomMetricsEventSource`来记录指标值。`CustomMetricsEventListener`以每秒输出一次指标的方式构建。

重要的是要理解是`CustomMetricsEventListener`的配置决定了度量报告间隔。这是通过向`EventCounterIntervalSec`参数传递一个以秒为单位指定时间间隔的数值来实现的。

```
class Program
{
    static void Main(string[] args)
    {
        var reader = new CustomMetricsEventListener();
        var arguments = new Dictionary<string, string>
        {
            {"EventCounterIntervalSec", "1"}
        };
        reader.EnableEvents(CustomMetricsEventSource.Log, EventLevel.LogAlways, EventKeywords.All, arguments);

        var random = new Random();
        for (int i = 0; i <= 10; i++)
        {
            SleepingBeauty(random.Next(10, 200));
        }

        Console.ReadKey();
    }

    static void SleepingBeauty(int sleepTimeInMs)
    {
        var stopwatch = Stopwatch.StartNew();

        Thread.Sleep(sleepTimeInMs);

        stopwatch.Stop();

        CustomMetricsEventSource.Log.ReportMethodDurationInMs(stopwatch.ElapsedMilliseconds);
        CustomMetricsEventSource.Log.ReportMetric("someCounter", DateTime.Now.Millisecond);
    }
} 
```

当我们运行该程序时，我们将看到向控制台报告的指标值:

> 计数器方法持续时间计数器报告值最小值:23，最大值:215，计数 8，平均值:113，125，标准偏差:64，10429，区间数:1，026394
> 
> 计数器某些计数器报告的值最小值:87，最大值:982，计数 9，平均值:603，1111，标准偏差:269，2832，区间数:1，026394

请记住，即使没有调用`EventCounter`实例的`WriteMetric`方法，也将总是以指定的时间间隔(使用`EventCounterIntervalSec`)报告指标。除了 *IntervalSec* 之外，所有数值都将具有默认值 0。

您可以在我的 GitHub 资源库中找到完整的工作示例:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ Expecho ](https://github.com/Expecho) / [博客](https://github.com/Expecho/Blog)

### 我在博客中提到的代码的占位符，请参见

<article class="markdown-body entry-content container-lg" itemprop="text">

# 博客

我在博客上写的关于的代码[的占位符](https://dev.to/expecho)

### 此存储库中的文件夹

*   度量:报告度量使用。净(核心)

</article>

[View on GitHub](https://github.com/Expecho/Blog)

# 接下来是什么？

当然，向控制台报告这些值并不会增加太多价值，而是由您决定将数据发送到您自己的日志后端。例如，您可以使用[eventcouncollectionmodule](https://docs.microsoft.com/en-us/azure/azure-monitor/app/eventcounters)将它们作为自定义指标发送到 Application Insights，记录到您的 ELK 堆栈或您正在使用的任何日志系统。

我真的希望报告指标能得到更多的关注，因为拥有可用的指标数据对于详细了解您的应用程序的行为非常重要。例如，您可以使用指标来扩大或缩小应用程序，或者深入了解应用程序某些部分的使用情况。据我所知，大多数日志框架都不支持度量标准。

关于是否支持使用的新的`ILogger`接口编写度量标准，有很多讨论。Net 核心，但最终没有支持它的权利知道。关于如何做和为什么做的更多细节可以在为这次讨论创建的问题中阅读:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 增加通过日志记录报告应用指标的支持 #708](https://github.com/aspnet/Logging/issues/708) 

[![anurse avatar](img/8f078a30b7107c205cbe2ad74e32becd.png)](https://github.com/anurse) **[anurse](https://github.com/anurse)** posted on [<time datetime="2017-09-19T21:09:47Z">Sep 19, 2017</time>](https://github.com/aspnet/Logging/issues/708)

## 概述

我们希望应用程序能够发出任意命名的指标，并让遥测/记录系统(App Insights、Elasticsearch/ELK 等)捕获它们。)以及其他度量系统(Statsd、InfluxDB 等。).为了允许应用程序在不耦合到指标的目的地的情况下发出指标，需要一个抽象，特别是在应用程序不直接依赖于应用程序见解的情况下，使用像应用程序见解自动点亮这样的功能。

## 提议

我们将为`ILogger`添加对指标报告的支持。这将通过一个新的配套接口`IMetricsLogger`来完成，记录器可能**可选地**实现该接口。应用程序可以通过这个接口发出指标(见下面的 API)，支持指标的日志提供程序可以接收和处理这些指标。度量是`(name, value)`元组，其中`value`总是浮点数。度量应该被集中查看，因此对度量使用像`object`这样的通用数据类型是没有意义的，所以度量值总是`double` s。支持任意类型的度量值不是目标。报告指标应该尽可能地高效，尤其是在没有注册任何记录器提供程序来监听它们或者它们被过滤掉的情况下。

### 为什么加到`ILogger`？

最初，我考虑开发一种新的度量抽象。然而，我遇到了一些问题:

1.  我们需要开发一个命名/组织度量的结构(可能使用类型/名称空间名称作为基础)
2.  我们需要开发一个过滤指标的结构(按类别、名称、粒度等)。)
3.  许多遥测系统支持日志和度量收集，并且必须提供新的系统
4.  消费者必须从 DI 获得两个不同的诊断报告(`ILogger<T>`、`IMetricReporter<T>`等)。)
5.  这将创建另一个诊断抽象(在 EventSource、EventCounters、DiagnosticSource、Perf Counters、yada yada yada 之上(看我在那里做了什么？；p))

事实上，上面的 1 和 2 已经存在于日志管道中，并且许多度量接收器也是日志接收器，这导致了将度量集成到日志中的想法，而不是创建一个全新的抽象。

我们**而不是**想把这个加到`ILogger`的主要原因是风险

## API

### `IMetricLogger`

用于`ILogger`实现的可选配套接口，指示它们对度量的支持。当且仅当希望接收度量时，由`ILoggerProvider`返回的`ILogger`实例被期望实现这个接口*。*

```
namespace Microsoft.Extensions.Logging
{
    public interface IMetricLogger
    {
        /// <summary>
        /// Define a new metric with the provided name and return an <see cref="IMetric"/> that can be used to report values for that metric.
        /// </summary>
        /// <param name="name">The name of the metric to define</param>
        /// <returns>An <see cref="IMetric"/> that can be used to report values for the metric</returns>
        IMetric DefineMetric(string name);
    }
}
```

### `IMetric`

允许报告指标数据的接口。度量值

```
namespace Microsoft.Extensions.Logging
{
    public interface IMetric
    {
        /// <summary>
        /// Record a new value for this metric.
        /// </summary>
        /// <param name="value">The value to record for this metric</param>
        void RecordValue(double value);
    }
}
```

### `LoggerMetricsExtensions`

在`ILogger`上提供了一个扩展方法，使消费者能够发出度量，即使底层的 logger 提供者不支持它(当然，在这种情况下度量将被忽略)。

```
namespace Microsoft.Extensions.Logging
{
    public static class LoggerMetricsExtensions
    {
        /// <summary>
        /// Define a new metric with the provided name and return an <see cref="IMetric"/> that can be used to report values for that metric.
        /// </summary>
        /// <remarks>
        /// If none of the registered logger providers support metrics, values recorded by this metric will be lost.
        /// </remarks>
        /// <param name="name">The name of the metric to define</param>
        /// <returns>An <see cref="IMetric"/> that can be used to report values for the metric</returns>
        public static IMetric DefineMetric(this ILogger logger, string name)
        {
            if(logger is IMetricLogger metricLogger)
            {
                return metricLogger.DefineMetric(name);
            }
            return NullMetric.Instance;
        }
    }
}
```

### `MetricValueExtensions`

对`IMetric`的扩展方法，以支持其他常见的度量值类型(如上所述，底层类型仍然是`double`，因此值必须可表示为`double`)。

**建议:我们可以有一个`.Time()` API 来返回一个`IDisposable`，它也使用秒表为你计时。不过，以后添加起来会很容易。**

```
using System;

namespace Microsoft.Extensions.Logging
{
    public static class MetricValueExtensions
    {
        /// <summary>
        /// Record a new value for this metric.
        /// </summary>
        /// <remarks>
        /// This is a convenience method that will convert the <see cref="TimeSpan"/> to a <see cref="double"/> via
        /// the <see cref="TimeSpan.TotalMilliseconds"/> property.
        /// </remarks>
        /// <param name="metric">The metric to record the value on.</param>
        /// <param name="value">The value to record for this metric.</param>
        public static void RecordValue(this IMetric metric, TimeSpan value) => metric.RecordValue(value.TotalMilliseconds);
    }
}
```

### `Logger`更新

我们从`LoggerFactory.CreateLogger`返回的聚合`Logger`类将被更新以支持`IMetricsLogger`，即使它聚合的`ILogger`都不支持它。它只会向已知支持该接口的记录器发送指标。

## `Define` / `Record`图案

为了尽可能降低记录实际指标值的性能成本，API 使用了一种`Define` / `Record`模式。消费者应该期望为特定的“foo”值调用`DefineMetric("foo")` **一次**，并尽可能全局地存储结果(以单例类型等)。).正是`DefineMetric`完成了建立度量记录的大部分工作。在调用`DefineMetric`之后，`IMetric`接口上的`RecordValue`调用可以根据提供者的需求选择如何存储值。例如，在一个计划发送预聚合指标的提供者中，`RecordValue`可以简单地更新滚动聚合值，然后删除实际记录(因此对每个指标有恒定的存储需求)。如果提供者需要报告单个指标，它可以使用动态分配的缓冲区或环形缓冲区，这取决于提供者的配置和需求。作为基于预聚合数据的`IMetric`实现的示例，请参见[https://gist . github . com/an urse/03c 6746204317 BD 3616 c 372 B4 df 9 dbba](https://gist.github.com/anurse/03c6746204317bd3616c372b4df9dbba)

## 过滤指标

~~指标应该是可过滤的，它们应该参与现有的日志程序过滤管道。我目前的提议是，将度量标准像`LogLevel.Critical`消息一样对待，因为如果类别被启用，度量标准就会被写入。我们可以考虑在`.DefineMetric`中捕获一个`LogLevel`，并使用它来提供不同“级别”的度量。由于现有的过滤器只允许您按照提供者、类别和级别进行过滤，所以很难按照名称过滤掉特定的指标(因为这需要一个新的过滤器结构)。这可能使得`LogLevel`对指标的支持更加重要。~~

*@pakrym 和我谈了谈，想出了一个具体的建议*

可以在提供者和类别名称上过滤指标，但是没有指标的`LogLevel`概念。然而，过滤器可以禁用特定提供商/类别组合的所有**指标。为此，我们将为默认为`true`的`LoggerFilterRule`添加一个`AllowMetrics`布尔值。在编写指标时，我们将根据过滤器选项检查提供者和类别名称，并检查这个布尔值。**

## 范围和属性包

**请求反馈**

我最初的想法是，度量存在于范围之外，因为它们通常是聚合的，而范围意味着每个事件的数据。从理论上讲，提供者可以让他们的`IMetric`实例读取活动范围数据，并根据他们的选择将它们附加到度量上(例如，报告非聚合度量的系统可能想要这样做)。出于类似的原因，属性包(即日志值/结构化日志记录等。)通常对指标没有意义，因为它们通常是聚合的。话虽如此，许多提供者确实支持任意属性(InfluxDB、App Insights)，因此某种级别的支持可能是有用的。我们可以很容易地为`.RecordValue`添加一个可选参数，允许为每个指标添加任意属性，如果对提供者没有意义，提供者可以选择忽略它。

## 对现有供应商的影响

对现有提供商的影响极小。因为该接口为提供者接收指标提供了一个选择加入模型，所以现有的提供者不受影响。我想像 Serilog 这样的提供商不会参与这个系统，因为他们没有度量的基础设施(/cc @nblumhardt，我可能错了！).像 App Insights 这样的提供者可以选择向他们现有的提供者添加指标支持，或者添加只处理共同注册的指标(并忽略日志消息)的新提供者。

[View on GitHub](https://github.com/aspnet/Logging/issues/708)