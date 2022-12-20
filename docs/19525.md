# 我如何学会不再担心并爱上服务定位器

> 原文：<https://dev.to/scotthannen/how-i-learned-to-stop-worrying-and-love-the-service-locator---4a32>

服务定位器是一种反模式。它们是我们在学习依赖注入之前使用的。如果你已经知道什么是服务定位器，为什么它是有害的(解释一个而不解释另一个是不道德的)，那么跳过第一个标题。

### 为什么服务定位器是邪恶的

为了简要说明什么是服务定位器以及它为什么不好，这里有一个注入了抽象依赖的“好”类:

```
 public class ClassWithADependency
    {
        private readonly IDoesSomethingINeed _somethingINeed;

        public ClassWithADependency(IDoesSomethingINeed somethingINeed)
        {
            _somethingINeed = somethingINeed;
        }
    } 
```

很容易看出这个类依赖于什么。这要看`IDoesSomethingINeed`。这个类很容易测试。我们可以使用模拟或测试 double 作为依赖项。

服务定位器是一个类，我们要求它为我们提供某种依赖的实例。它可以是实例化类或静态类。我们可以这样称呼它:

```
 public class ClassWithADependency
    {
        public void Blarg()
        {
            var somethingINeed = ServiceLocator.GetService<IDoesSomethingINeed>();
            somethingINeed.DoIt();
        }
    } 
```

似乎很方便。我们创建类实例的逻辑隐藏在`ServiceLocator`中。但是有几个大问题:

首先，不再容易区分任何类依赖于什么。如果我在构造函数中注入依赖项，很容易判断出当我的类有五个、六个或十几个依赖项时，它是否失去了控制。服务定位器允许我们隐藏对任何方法中任何事物的依赖。我可以这样做:

```
 private SomeMethodDeepWithinMyClass()
    {
        var whatIsThis = ServiceLocator.GetService<SomethingTotallyUnrelatedToWhatThisClassShouldDo>(); 
        whatIsThis.DoWhoKnowsWhat();
    } 
```

这使得通过到处添加随机依赖项来使代码复杂化和耦合变得更加方便。服务定位器就像一扇[破碎的窗户](https://en.wikipedia.org/wiki/Broken_windows_theory)。一旦一个开发人员这样做了，其他人就会加入进来。

第二，一个充斥着对服务定位器的调用的类很难测试。如果服务定位器是一个静态类，那么在单元测试中，您必须配置该静态类以返回模拟或测试 doubles，这比在需要的地方注入模拟要复杂得多。或者，如果服务定位器本身是作为依赖注入的，那么我们必须创建一个返回更多模拟的模拟，如下所示:

```
 var somethingINeedMock = new Mock<IDoesSomethingINeed>();
    somethingINeedMock.Setup(x => x.GetSomeValue()).Returns("theValue");
    var serviceLocatorMock = new Mock<IServiceLocator>();
    serviceLocatorMock.Setup(x => x.Resolve<IDoesSomethingINeed>()).Returns(somethingINeedMock.Object); 
```

一开始并不是灾难性的，但很快就会失控。我见过这样的情况，开发人员放弃了，因为他们无法跟踪一个类从服务定位器获得了什么依赖，所以他们使用创建服务定位器模拟的设置方法，这些模拟返回所有的模拟，不管单个测试是否需要它。在这一点上，几乎不可能说出您实际测试的是什么。

(您可能已经注意到，对服务定位器的调用看起来很像是从依赖注入容器中解决问题的调用。事实上，如果我们从大多数类中直接调用容器，那么这就是将容器用作服务定位器。)

### 当我们需要服务定位器时

为了正确使用依赖注入，我们需要控制如何在组合根创建类。ASP.NET 核心控制器就是一个例子。它们是为处理传入请求而创建的类，在应用程序启动时，我们可以对它们进行配置，以期望注入某些依赖项。如果这些依赖项有它们自己的依赖项，我们也注入它们。一路注射毒品。控制器不需要直接解析来自`ServiceProvider`的东西。NET Core 的依赖注入容器)，像服务定位器一样使用它。

有时正确配置依赖注入并不是一个选项。如果我们在一个旧的 WebForms 应用程序中工作呢？WebForms 最近进行了更新，以支持依赖注入，但如果您正在使用旧的应用程序，这可能为时已晚。

或者，如果您需要在一个现有的类上工作，而复合根无可救药地被破坏得无法理解，该怎么办？假设您需要修改的类如下所示:

```
 public class ClassINeedToModify : ItInheritsFromThis
    {
        public ClassINeedToModify(
            DependsOnThis thing, 
            AndThis otherThing, 
            KitchenSink kitchenSink) 
            : base(kitchenSink)
        {
            thing.DoSomethingEvil(otherThing);
        }
    } 
```

顺便说一下，`ItInheritsFromThis`是一个你不能修改的巨型 god 类，因为一半的应用程序依赖于它。这一切都是通过调用构造函数来创建的，这些构造函数使用了更多静态类的值，没有依赖注入容器。

现在你不走运了。您希望使用小型的、设计合理的、可测试的、采用依赖注入的类来添加新的功能，但是应用程序的组合根的糟糕状态使得不可能将新的依赖恰当地注入到现有的类中。

我们该怎么办？放弃，堆在烂摊子上？这里有几个选择。

### 依赖注入与默认值

这可能会让我们到达我们需要的地方:

```
 public class ClassINeedToAddToTheMess
    {
        private readonly IDependsOnThis _dependency;

        public ClassINeedToAddToTheMess(IDependsOnThis dependency = null)
        {
            _dependency = dependency ?? new DependsOnThis();
        }
    } 
```

现在我们可以实例化一个新的`ClassINeedToAddToTheMess`实例，它将创建自己的默认依赖项，但是我们可以使用模拟来测试这个类。

这在一些情况下就足够了，在这些情况下，我们没有很多嵌套的依赖关系，也不需要做任何复杂的事情。

但是如果我们需要使用命名组件和抽象工厂构造更复杂的依赖关系——依赖注入容器有助于这种事情，该怎么办呢？

### 创建自己的组合根，并用服务定位器公开它

是的，我建议您通过有意和有意地实现一个公认的反模式，将自己暴露在轻蔑、嘲笑和自我厌恶的境地。也许你会觉得更好，如果你加上这样的评论:

```
 // I’m so sorry that I had to implement a service locator. There was no other way. Forgive me! 
```

如果你这样写，你可能会感觉更好:

```
 /* Yes, this is a service locator. I’m not happy about it. But it wasn’t my idea to mangle 
     * the composition root so thoroughly that it’s impossible to properly inject anything into 
     * this third-level inherited god class, which by the way is also an anti-pattern. 
     */ 
```

一种更愉快的方式是，您正在创建一个新的复合根，从这个根解析您的类。

我们怎么做呢？就像我们要正确配置和使用依赖注入容器一样。我们可以从创建一个配置容器的类开始。这篇文章很大程度上致力于这个话题。

例如，使用 [Autofac](https://autofac.org/) :

```
 public class ClassINeedToAddAutofacModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<ClassINeedToAddToTheMess>().As<IClassINeedToAddToTheMess>();
            builder.RegisterType<DependsOnThis>().As<IDependsOnThis>();
        }
    } 
```

注意它是特定的。我们正在为我们不幸的 god 类创建一个单独的依赖项，所以我们将此限制为配置那个依赖项及其依赖项。额外的好处是，如果应用程序被重构，这是可重用的，这样我们就可以按照自己喜欢的方式使用依赖注入容器。

如果我们需要注册一个对 god 类创建的东西的依赖怎么办？例如，god 类使用它创建的 [`TelemetryClient`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) 来写日志数据，但是我们不希望我们的新类耦合到`TelemetryClient`。我们可以利用这一点。

首先，我们创建我们想要依赖的抽象:

```
 public interface ILogger
    {
        void LogException(Exception ex);
    } 
```

然后我们创建一个依赖于`TelemetryClient`的实现:

```
 public class TelemetryClientLogger : ILogger
    {
        private readonly TelemetryClient _telemetryClient;

        public TelemetryClientLogger(TelemetryClient telemetryClient)
        {
            _telemetryClient = telemetryClient;
        }

        public void LogException(Exception ex)
        {
            _telemetryClient.TrackException(ex);
        }
    } 
```

然后我们修改我们的模块，使它依赖于一个`ILogger`。

```
 public class ClassINeedToAddAutofacModule : Module
    {
        private readonly ILogger _logger;

        public DoesSomethingINeedAutofacModule(ILogger logger)
        {
            _logger = logger;
        }

        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<ClassINeedToAddToTheMess>().As<IClassINeedToAddToTheMess>();
            builder.RegisterType<DependsOnThis>().As<IDependsOnThis>();
            builder.Register(c => _logger).As<ILogger>();
        }
    } 
```

现在，在我们的复合根中创建的类可以依赖于抽象。

### 插入我方服务定位器

这是稍微令人不快的部分。我们的服务定位器可以是实例化的类，也可以是静态类。这真的没有关系，因为无论哪种方式，它都不会在我们的上帝类的上下文中被测试。记住，我们没有制造那个问题。这个想法是注入依赖项而不是实例化它们，但是事实上我们不能这样做，这就是我们走这条路的原因。现有代码可能不可测试。我们只是尽最大努力确保我们与之集成的*新*类是可组合的和可测试的。

这是服务定位器类。我考虑称它为“工厂”，因为它听起来更好，但我们就叫它什么吧。

```
 public class DoesSomethingINeedLocator
    {
        private readonly IContainer _container;

        public DoesSomethingINeedLocator(ILogger logger)
        {
            var containerBuilder = new ContainerBuilder();
            containerBuilder.RegisterModule(new ClassINeedToAddAutofacModule(logger));
            _container = containerBuilder.Build();
        }

        public IDoesSomethingINeed Locate()
        {
            return _container.Resolve<IDoesSomethingINeed>();
        }
    } 
```

为了减少麻烦，服务定位器只显式返回一种依赖关系。没有`Get<TAnything>()`功能。

现在，已经在创建和使用自己的`TelemetryClient`的 god 类可以这样调用:

```
 var thingINeedLocator = new DoesSomethingINeedLocator(_telemetryClientItAlreadyCreated);
    var thingINeed = thingINeedLocator.Locate(); 
```

因为我们正在创建一个类实例——调用`new`来实例化一个新对象——我们不能模仿这种依赖性，所以 god 类的可测试性较差。代价是它返回的类——所有的新东西——使用依赖注入，是可测试的，并且没有对容器的任何引用。

我们希望我们的组合根及其对容器的所有引用都在应用程序启动时，远离应用程序的业务端。既然做不到，我们就在需要的地方种了新的作文根，尽最大努力保持整洁。

### 可测试

当我们使用依赖注入容器构造类时，如果我们错过了注册一个依赖项，就会得到一个运行时错误。我们不需要运行应用程序并等待一个异常来查看我们是否错过了什么。我们可以编写一个单元测试:

```
 [TestMethod]
    public void AutofacModuleResolvesThingINeed()
    {
        var logger = new Mock<ILogger>();
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule(new ClassINeedToAddAutofacModule(logger.Object));
        using (var container = containerBuilder.Build())
        {
            container.Resolve<IClassINeedToAddToTheMess>();
        }
    } 
```

### 值得吗？

如果您需要创建的新类很复杂，并且包含许多嵌套的依赖项，那么是的。

这种方法令人满意，因为它在我工作的遗留代码和我正在编写的新代码之间划了一条线。遗留类不允许我注入依赖项，但是我的新类允许。服务定位器是它们之间的桥梁。这让我能够以我习惯的方式工作，创建小的、可测试的类，我可以使用依赖注入容器来组合这些类。