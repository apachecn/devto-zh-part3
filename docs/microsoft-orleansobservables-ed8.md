# 微软奥尔良—可观察

> 原文：<https://dev.to/kritner/microsoft-orleansobservables-ed8>

### 微软奥尔良—观察家

<figcaption>Photo by [Freddy Marschall](https://unsplash.com/@freddymarschall?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

随着我们继续探索微软 Orleans——虚拟演员模型框架——我们遇到了观察者。例如，观察器可以用于以通知的形式“观察”事件。

Orleans observers 是通过创建一个实现 Orleans' namespaces' `IGrainObserver`接口的接口来构建的。Observer 方法必须有一个 void 返回类型，并且当“某事”发生时，可以通过方法调用的形式调用。

可以在以下位置找到《奥尔良观察家》的文档:

[观察者|微软奥尔良文档](https://dotnet.github.io/orleans/Documentation/grains/observers.html)

#### 我们需要什么

设置观察器和可以管理观察器的颗粒有几个步骤:

*   实现`IGrainObserver`的新接口，以及实现新接口的类。
*   一个新的粒度接口和粒度，提供了订阅、取消订阅和通知等方法。
*   管理注册观察者的类。
*   一种使用上述观测器的方法。

#### 实现 IGrainObserver 的新接口

为了最直观地观察，我们将创建一个只接受字符串消息的接口(并最终创建一个类)。该界面将类似于:

```
public interface IObserverSample : IGrainObserver
{
 void ReceiveMessage(string message);
} 
```

在上面的例子中，我们有一个方法，它接受一个名为 message 的字符串。这个接口将作为我们的“观察者”接口。如您所见，这个接口非常简单——唯一的约束是 observer 方法需要有一个 void 返回类型，并且接口本身必须实现内置的 Orleans 类型的`IGrainObserver`。

#### 新增可观察颗粒界面

接下来，我们需要一个能够处理观察者注册和注销的 grain 接口，以及一个应该用来“通知”注册的观察者将要被观察的事件的方法。

```
public interface IObservableManager : IGrainWithIntegerKey, IGrainInterfaceMarker
{
 Task Subscribe(IObserverSample observer);
 Task Unsubscribe(IObserverSample observer);
 Task SendMessageToObservers(string message);
} 
```

同样非常简单——我们有一个`Subscribe`和`Unsubscribe`方法，它们接受一个`IObserverSample`(上一步的接口)和一个`SendMessageToObservers`，奇怪的是，它们可以用来向注册的观察者发送消息。

#### 一个管理注册观察员的类

[文档](https://dotnet.github.io/orleans/Documentation/grains/observers.html)使用一个内置类`ObserverSubscriptionManager`来帮助管理观察者，然而这个类显然被移到了一个遗留程序集中。在一些 Orleans 示例中仍然可以找到该类，下面是该类的一些调整:

```
public class GrainObserverManager<T> : IEnumerable<T> where T : IAddressable
{
 private readonly Dictionary<T, DateTime> observers = new Dictionary<T, DateTime>();

 public GrainObserverManager()
 {
  this.GetDateTime = () => DateTime.UtcNow;
 }

 public Func<DateTime> GetDateTime { get; set; }

 public TimeSpan ExpirationDuration { get; set; } = TimeSpan.FromMinutes(1);

 public int Count => this.observers.Count;

 public void Clear()
 {
  this.observers.Clear();
 }

 public void Subscribe(T observer)
 {
  // Add or update the subscription.
  this.observers[observer] = this.GetDateTime();
 }

 public void Unsubscribe(T observer)
 {
  this.observers.Remove(observer);
 }

 public async Task Notify(Func<T, Task> notification, Func<T, bool> predicate = null)
 {
  var now = this.GetDateTime();
  var defunct = default(List<T>);
  foreach (var observer in this.observers)
  {
   if (observer.Value + this.ExpirationDuration < now)
   {
    // Expired observers will be removed.
    defunct = defunct ?? new List<T>();
    defunct.Add(observer.Key);
    continue;
   }

   // Skip observers which don't match the provided predicate.
   if (predicate != null && !predicate(observer.Key))
   {
    continue;
   }

   try
   {
    await notification(observer.Key);
   }
   catch (Exception)
   {
    // Failing observers are considered defunct and will be removed..
    defunct = defunct ?? new List<T>();
    defunct.Add(observer.Key);
   }
  }

  // Remove defunct observers.
  if (defunct != default(List<T>))
  {
   foreach (var observer in defunct)
   {
    this.observers.Remove(observer);
   }
  }
 }

 public void Notify(Action<T> notification, Func<T, bool> predicate = null)
 {
  var now = this.GetDateTime();
  var defunct = default(List<T>);
  foreach (var observer in this.observers)
  {
   if (observer.Value + this.ExpirationDuration < now)
   {
    // Expired observers will be removed.
    defunct = defunct ?? new List<T>();
    defunct.Add(observer.Key);
    continue;
   }

   // Skip observers which don't match the provided predicate.
   if (predicate != null && !predicate(observer.Key))
   {
    continue;
   }

   try
   {
    notification(observer.Key);
   }
   catch (Exception)
   {
    // Failing observers are considered defunct and will be removed..
    defunct = defunct ?? new List<T>();
    defunct.Add(observer.Key);
   }
  }

  // Remove defunct observers.
  if (defunct != default(List<T>))
  {
   foreach (var observer in defunct)
   {
    this.observers.Remove(observer);
   }
  }
 }

 public void ClearExpired()
 {
  var now = this.GetDateTime();
  var defunct = default(List<T>);
  foreach (var observer in this.observers)
  {
   if (observer.Value + this.ExpirationDuration < now)
   {
    // Expired observers will be removed.
    defunct = defunct ?? new List<T>();
    defunct.Add(observer.Key);
   }
  }

  // Remove defunct observers.
  if (defunct != default(List<T>))
  {
   foreach (var observer in defunct)
   {
    this.observers.Remove(observer);
   }
  }
 }

 public IEnumerator<T> GetEnumerator()
 {
  return this.observers.Keys.GetEnumerator();
 }

 IEnumerator IEnumerable.GetEnumerator()
 {
  return this.observers.Keys.GetEnumerator();
 }
} 
```

注意，我在 Orleans github repo 上找到的原始类(在它们的样本下):

[点网/奥尔良](https://github.com/dotnet/orleans/blob/6b802f6365e78d691430360a4101b1993eb7afee/Samples/2.1/ServiceFabric/Grains/GrainObserverManager.cs)

#### 新粮实施

我们已经为我们的观察者/可观察对象创建了所有的基础和抽象——现在我们需要这些接口的具体化。

引入的一个新颗粒处理 sub/unsubbing，以及通知订阅的观察者的“事件”。这个纹理应该看起来比较熟悉:

```
public class ObservableManager : Grain, IObservableManager, IGrainMarker
{
 private GrainObserverManager<IObserverSample> _subsManager;

 public override async Task OnActivateAsync()
 {
  _subsManager = new GrainObserverManager<IObserverSample>();
  await base.OnActivateAsync();
 }

 public Task SendMessageToObservers(string message)
 {
  _subsManager.Notify(n => n.ReceiveMessage(message));
  return Task.CompletedTask;
 }

 public Task Subscribe(IObserverSample observer)
 {
  _subsManager.Subscribe(observer);
  return Task.CompletedTask;
 }

 public Task Unsubscribe(IObserverSample observer)
 {
  _subsManager.Unsubscribe(observer);
  return Task.CompletedTask;
 }
} 
```

在上面的文章中，唯一没有提到的新东西(非常确定)是`OnActivateAsync`的重写。在这个方法中，我们更新了`_subsManager`并继续基本实现。

`Subscribe`和`Unsubscribe`方法注册或移除从`GrainObserverManager`传入的`IObserverSample`，而`Notify`方法向所有订阅的观察者发送事件通知。

#### 新功能

在本演示中，将引入两个新的`IOrleansFunctions`。其中一个函数将用作观察器，另一个函数将用于向该观察器发送消息。

从两者中较简单的开始，事件发送者:

```
public class GrainObserverEventSender : IOrleansFunction
{
 public string Description => "This function can be used to send a message to subscribed observers.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  var grain = clusterClient.GetGrain<IObservableManager>(0);

  Console.WriteLine("Enter a message to send to subscribed observers.");
  var message = Console.ReadLine();

  await grain.SendMessageToObservers(message);

  ConsoleHelpers.ReturnToMenu();
 }
} 
```

在上面的例子中，我们使用了前面定义的 grain 接口的三种方法之一。从这里开始，我们只是利用这个函数将用户输入的消息发送给我们订阅的观察者(如果有的话)。

我们如何让观察者存在？这可以通过第二个`IOrleansFunction`来完成。

```
public class GrainObserverReceiver : IOrleansFunction, IObserverSample
{
 private bool _shouldBreakLoop;

 public string Description => "Acts as a receiver of observed messages. When the observer manager notifies subscribed observers like this class, they take action on the notification.";

 public async Task PerformFunction(IClusterClient clusterClient)
 {
  Console.WriteLine("Observing for behavior, stops once behavior observed.");

  var observerManager = clusterClient.GetGrain<IObservableManager>(0);
  var observerRef = await clusterClient
   .CreateObjectReference<IObserverSample>(this);

  while (!_shouldBreakLoop)
  {
   await observerManager.Subscribe(observerRef);
   await Task.Delay(5000);
  }

  await observerManager.Unsubscribe(observerRef);

  ConsoleHelpers.ReturnToMenu();
 }

 public void ReceiveMessage(string message)
 {
  ConsoleHelpers.LineSeparator();
  Console.WriteLine("Observed Behavior:");
  Console.WriteLine(message);

  _shouldBreakLoop = true;
 }
} 
```

上面发生的几件新鲜事`IOrleansFunction`。首先，我们的`PerformFunction`方法被用来偶尔订阅我们的观察者管理器 grain——这是作为一种“心跳”来保持观察者存活。我不认为*有*可以这样做，但是使用文档中的样本代码，这看起来还不错。我想另一种选择是不让观察者过期，并让他们无限期地存在下去？在上面的例子中，我们进行了正常的 GetGrain 调用，但是另外，我们将它设置为一个 observer 引用，并向 observer manager 注册。

另一个方法`ReceiveMessage`是从`IObserverSample`开始实现的方法。这个方法是当来自观察者管理器的`Notify`被调用时发生的事情的处理程序。

#### 演示观察者

现在剩下的就是运行应用程序并确保它正常工作！在这个演示中，我们将像往常一样运行 silohost 和 client，不过这次我们实际上将运行两个客户端。一个客户端将用作观察器，另一个将用于向观察器发送消息。这看起来像什么？

[https://www.youtube.com/embed/4oSrF21WfOk](https://www.youtube.com/embed/4oSrF21WfOk)

这篇文章的代码可以在这里找到:

[krit ner-Blogs/OrleansGettingStarted](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.55)

#### 相关:

*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   [微软奥尔良—报告仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199)
*   使用多态性更新新奥尔良项目，为新奥尔良范例做好准备！
*   [微软奥尔良—依赖注入](https://medium.com/@kritner/microsoft-orleans-dependency-injection-6379d52a7169?source=friends_link&sk=6c3883a5213d65eb251b56c717e0e4f2)
*   [微软奥尔良——在“开发”和“生产”配置之间轻松切换。](https://medium.com/@kritner/microsoft-orleans-easily-switching-between-development-and-production-configurations-20e109be6458?source=friends_link&sk=1e8fc6aa072a5b293d029c00012165b3)
*   [。网络核心控制台应用程序选项配置](https://medium.com/@kritner/net-core-console-application-ioptions-t-configuration-ae74bfafe1c5?source=friends_link&sk=c5bcab4f7f10c97175ad68fc12cb9cc6)
*   [微软文档—奥尔良观察家](https://dotnet.github.io/orleans/Documentation/grains/observers.html)
*   发布时的代码—[https://github . com/krit ner-Blogs/OrleansGettingStarted/releases/tag/v 0.55](https://github.com/Kritner-Blogs/OrleansGettingStarted/releases/tag/v0.55)