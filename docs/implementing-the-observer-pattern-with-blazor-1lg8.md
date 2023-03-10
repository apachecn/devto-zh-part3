# 用 Blazor 实现观察者模式

> 原文：<https://dev.to/rembou1/implementing-the-observer-pattern-with-blazor-1lg8>

观察者模式对于解耦组件来说是一个非常有趣的模式。它非常适用于解耦 UI 和业务代码:当业务方面发生变化时，更新您的 UI。

ASPNET(核心或框架)开发人员在服务器端很少使用这种模式:每个请求启动一个新状态，实例是短暂的，管理事件订阅太麻烦了。在服务器端，我们更多地使用 pubsub 模式或事件聚合器，它们是从 observer 派生出来的，但是使用了不同的技术。

在 Blazor 中，这种模式对于同步组件很有意义，就像在 WPF 或 Unityor WinForms 中一样。在这篇博文中，我将展示如何实现它。

## 服务

客户端应用程序是有状态的，这意味着在每个动作之后，状态被保存在内存中以允许其他动作。在这种情况下，主要对服务使用 singleton 是有意义的，这样它们可以在内部保持应用程序的状态。这是我的服务发布了一个事件

```
public class MyService : IMyService{
  public event EventHandler<string> OnDataUpdated;
  private string _data;
  public void UpdateDate(string newData){
    this._data = newData;
    this.OnDataUpdated?.Invoke(this,newData);
  }
} 
```

*   我在 C#中使用了 event 关键字，它是专门为实现这种模式而设计的
*   EventHandler 的一般参数在这里是一个字符串，但是如果需要嵌入更多数据，您可以创建特定的有效负载类
*   我用“？”因为如果没有人订阅该事件，OnDataUpdated 将为空，并且发布者永远不知道是否有订阅者
*   Invoke 的第一个参数是事件的发送者，所以大多数时候我们会发送“this”。说出是谁创建了这个事件(订阅者和发布者应该忽略彼此)有点反模式，但是 MSFT 的人一定有很好的理由这样做。
*   该事件也可以由其他服务发布，因为 OnDataUpdated 是公共的，我们可以只允许公共订阅这种符号的事件[https://docs . Microsoft . com/en-us/dot net/cs harp/programming-guide/events/how-to-implementation-custom-event-accessors](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/how-to-implement-custom-event-accessors)。

## 组件

这里有一个订阅该事件的简单组件，显示数据最后更新的时间

```
@implements IDisposable
@inject IMyService myService;

@lastUpdate

@functions{
    DateTime lastUpdate;
    protected override void OnInit()
    {
        myService.OnDataUpdated += Handle;
        base.OnInit();
    }
    protected void Handle(object sender, string args)
    {
       lastUpdate = DateTime.Now;        
    }
    public void Dispose()
    {
        myService.OnDataUpdated -= Handle;
    }
} 
```

*   在 init 中，“+=”符号订阅事件
*   非常重要的一点:我必须让 component 实现 IDisposable，这样我就可以在事件被移除时取消订阅。如果我没有这样做，那么代表我的组件的对象将永远保存在内存中(因为我的服务是单例的)，如果你多次创建/删除你的组件，这将使你的应用程序占用越来越多的内存。这里有一个来自尊敬的乔恩·斯基特的关于此事的伟大帖子[https://stackoverflow.com/a/4526840/277067](https://stackoverflow.com/a/4526840/277067)。

## 结论

这种模式对于在保持组件解耦的同时同步应用程序中的组件非常有用。这再次表明 Blazor 是一个伟大的项目，因为你可以使用 C#和. Net 中已经存在的所有好东西。开发可能只在几个月前开始，但你可以重用的东西是巨大的。

## 来源

-[https://docs . Microsoft . com/en-us/dot net/cs harp/programming-guide/events/how-to-subscribe-to-unsubscribe-from-events](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/how-to-subscribe-to-and-unsubscribe-from-events)