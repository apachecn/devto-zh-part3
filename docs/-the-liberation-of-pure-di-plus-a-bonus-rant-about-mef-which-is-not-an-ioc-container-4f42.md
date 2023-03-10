# 纯 DI 的解放(外加一个关于 MEF 的奖励，它不是 IoC 容器)

> 原文：<https://dev.to/scotthannen/-the-liberation-of-pure-di-plus-a-bonus-rant-about-mef-which-is-not-an-ioc-container-4f42>

了解依赖注入和使用依赖注入/IoC 容器之间的区别很重要。依赖注入是编写类，使它们*接收*依赖，而不是创建它们或引用静态类。容器帮助您创建这些类的实例。

举例说明(原谅我，我真的需要学习如何言简意赅，只提一个概念而不解释):

```
 public class MyClassThatNeedsDependency
    {
        private readonly IThingINeed _thing;

        public MyClassThatNeedsDependency(IThingINeed thing)
        {
            _thing = thing; // dependency injection - good!
        }

        public void DoSomething()
        {
            _thing.MakeMagicHappen();
            var someOtherThing = new SomeOtherThingINeed(); // creates an instance of a concrete 
            someOtherThing.MakeOtherMagicHappen();          // implementation - bad
        }
    } 
```

`IThingINeed`被注射。该类需要向其构造函数提供一个实例。因为类*接收*一个`IThingINeed`而不是创建一个实现，所以它与`IThingINeed`的任何实现完全解耦。那很好。当我们为这个类编写单元测试时，我们可以模仿`IThingINeed`。

然后它创建一个`SomeOtherThingINeed`的实例。这可能是不好的。因为它做到了这一点，所以这个类与那个类紧密耦合。如果不使用或测试`SomeOtherThingINeed`，我们就不能使用或测试`MyClassThatNeedsDependency`。换`SomeOtherThingINeed`也比较难。如果我们想添加构造函数参数呢？这意味着创建`SomeOtherThingINeed`实例的每个类都必须被修改以提供这些参数。这些类需要知道从哪里获取这些参数的值。紧密耦合变得更加紧密，我们的代码慢慢变得更难维护。

这就是依赖注入。我们接收依赖项，而不是创建它们，这使得每个类可以自由地做自己的事情，而不必担心它的依赖项如何做它们的事情。

IoC 容器是帮助我们创建使用依赖注入的类的实例的工具。例如，假设一个类依赖于`ISomething`，但是`ISomething`的实现是一个有三个以上依赖的类。其中一些依赖关系有依赖关系，等等。结果是每个单独的类在隔离时更简单(这正是我们想要的)，但是创建一个类的实例现在更复杂了。构造函数调用可能如下所示:

```
 IHouse house = new FloorPlan23House(
        new ElectricalSystem(
            new GeModel3000WiringPanel(), 
            new SolarPowerSource(new acmeSolarPanelArray(New Sun()), new MassiveBattery())), 
        new Kitchen(new MarbleCounterTop(new HomeDepotSink().... 
```

如果你正在处理大量的类，这可能变得难以理解。像 Windsor、Autofac、Unity 或 Ninject 这样的 IoC 容器使它更易于管理。您可以编写如下所示的代码:

```
 var container = new WindsorContainer();
    container.Register( 
        Component.For<IHouse, Floorplan23House>(), 
        Component.For<IPowerSupply, SolarArray>(), 
        Component.For<ISolarPanel, Model3000SolarPanel>(), 
        Component.For<IBattery, MassiveBattery>(),
        // lots more
    );
    IHouse house = container.Resolve<IHouse>(); 
```

只要您已经为构造函数中所需的每种类型指定了具体的类或实例，它就会调用所有的构造函数来创建新对象，并将它们传递给其他构造函数，依此类推，直到创建出您需要的“顶级”类。如果有一个你还没有“注册”的类型，它会抛出一个有用的异常，说“我正试图创建一个需要`ISomething`的类，但是你还没有告诉我实现是什么。”令人高兴的是，您可以在每个类自己的上下文中使用它们，而不必考虑当时您不关心的其他类的细节。(您还可以组织所有这些设置代码，以便更容易管理，并编写测试，以便不会出现运行时错误。如果异常是有帮助的，那很好，但是如果你在单元测试中发现它们，它们会有 100 倍的帮助。)

### 输入 MEF，它不是 IoC 容器

这让我想到了微软的托管可扩展性框架，简称 MEF。在我发现自己在一个只有依赖注入容器被允许的环境中工作之前，我度过了许多没有它的快乐时光。(如果你熟悉 MEF，而那句话毫无意义，不要担心——这个故事结束时我已经追上你了。)我是在用 Windsor 写完我所有的配置后发现这一点的。幸运的是，我的代码没有以任何方式耦合到 Windsor，所以我只需要改变我的 DI 配置，而不是我的“真正的”代码。

坦白地说，我并没有非常努力地想弄清楚如何像使用“普通”依赖注入容器一样使用 MEF，这样我就可以像上面的例子一样编写代码。我给自己 30 分钟去找。我使用过许多 IoC 容器，尽管我倾向于 Windsor，但在撰写博客帖子或回答 StackOverflow 问题时，我也喜欢尝试其他容器。它们就像中国的自助餐——每一种都不一样，但是如果你去过一个，你会认出你在另一个看到的东西。(这是我最喜欢中国自助餐的一点。)相信我，30 分钟是合理的。试试看。给自己 30 分钟的时间来寻找使用 Autofac 作为 IoC 容器的说明。您将只需要大约 30 秒(因为 Autofac 是一个 IoC 容器。)

在 30 分钟内，我找到了大量关于如何使用 MEF 的页面，但是没有一个解释如何将它作为一个简单的 IoC 容器使用。我尝试了官方文档，在那里我找到了类似这样的东西:

```
 [Import(typeof(ICalculator))]
    public ICalculator calculator;

    [Export(typeof(ICalculator))]
    class MySimpleCalculator : ICalculator
    {
    } 
```

什么...这...实际的...我永远不会把这些属性放在任何东西上。也许在一个平行宇宙中，一个邪恶版本的我，带着胡子和/或眼罩，会给实现`ICalculator`的类加上一个属性，说明，是的，它确实实现了`ICalculator`，这样其他地方的一些怪异的巫毒可以将一个类实例分配给一个公共的*字段*...等等，这甚至不是构造函数注入。这是*地产*的注射剂！这里是我的内部成员，公开暴露在风中，以便 MEF *或其他任何东西*可以设置它们，我将编写代码来表现好像这是在对象生命周期中的某个不确定点发生的！我知道有这样的场景，但是考虑到构造函数注入在本文档中从未被提及，为什么有人会把 MEF 作为实现它的首选呢？我开始觉得 MEF 不是问题所在。

再往下:

```
 [Export(typeof(IOperation))]
    [ExportMetadata("Symbol", '+')]
    class Add: IOperation
    {
        public int Operate(int left, int right)
        {
            return left + right;
        }
    } 
```

停下，我再也受不了了。我已经准备好接受这是我的极度无知，但是我甚至不想知道`[ExportMetadata("Symbol", '+')]`是什么意思，或者为了让它有意义，我可能必须在其他地方做些什么。我当然不会把它强加给我之后的开发人员。

顺便说一下，猜猜如果你把`[Export(typeof(IOperation))]`放在一个没有实现`IOperation`的类上，编译器会做什么？如果你什么也没猜到，这只是说一个类实现了一个接口，而它没有，这样你就能得到一个运行时错误，而不是编译器错误，你就是赢家。

我现在对我自己有点苛刻，但这里有几点为它辩护:

*   我引用的的[文档不包括“依赖注入”或“控制反转”这样的词。我不认为这是个错误。](https://docs.microsoft.com/en-us/dotnet/framework/mef/)
*   如前所述，代码示例与任何流行的依赖注入容器都不相似(所有这些都彼此相似)。)
*   微软为实现构造函数注入提供了一个“普通”的容器。叫做[合一](https://github.com/unitycontainer/unity)。另一个是 ASP.NET 核心的默认设置，但是您可以在任何类型的项目中使用它。它们都被明确描述为实现依赖注入的工具。我觉得微软并不迷茫。

这些线索可能表明 MEF 是一个糟糕的 IoC 容器，因为*它不是一个*并且真正的问题是有人不仅认为它是一个 IoC 容器，而且认为它应该优先于所有其他容器？

我试图通过查看当前在代码中如何使用 MEF 来澄清这种混乱，并找到了如下内容:

```
 RegistrationBuilder registrations = new RegistrationBuilder();
    registrations.ForTypesMatching(r => r.Name.EndsWith("Foo"))
        .SetCreationPolicy(CreationPolicy.NonShared)
        .ExportInterfaces()
        .Export();
    registrations.ForTypesMatching(r => r.Name.EndsWith("Blarg"))
        .SetCreationPolicy(CreationPolicy.NonShared)
        .ExportInterfaces()
        .Export();
    catalog.Catalogs.Add(new AssemblyCatalog(typeof(FooBlarg).Assembly, registrations)); 
```

让我们来分解一下由此引发的一些有趣的问题:

*   如果你想知道我们是否在寻找名字以`Foo`结尾的类来实现名字也以`Foo`结尾的接口，我也是。我有 53%的把握这是正确的。
*   我们正在搜索的程序集中有多少类实现以`Foo`和`Blarg`结尾？答案分别是两个和一个。
*   如何找出这配置了哪些接口和类？你*在你的代码*文本中搜索`Foo`和`Blarg`。
*   我们已经知道我们想要使用哪些接口和实现(因为它们在同一个项目中的一个类中)，那么我们为什么不至少使用它们的全名，而不是让开发人员搜索以后缀结尾的类，并在找到它们时，希望他们已经正确地猜到了这段代码在做什么？也许是因为我们在一个教程里看到的，看起来很酷？
*   你能右击一个类定义来看看它在哪里被使用吗？不，因为类只有在通过其名称的一部分发现类型后才被实例化。
*   我是否提到过这些类型不是在动态加载的程序集中发现的(MEF 存在的那种场景)，而是存在于与“发现”它们的代码完全相同的项目中(包含不到 10 个类，包括搜索其他类的那个)?)我不需要这样做，因为您可能已经注意到，上面的代码包含了显式的指令，只在包含类型`FooBlarg` 的程序集中搜索名称以`Blarg` *结尾的类型，这正是这段代码要“发现”的类型*
*   这是不是就像编写代码从咖啡杯的当前位置开始搜索咖啡杯，然后搜索所有占用相同物理空间的咖啡杯？你着火了。

如果您没有显式引用程序集(假设这些类型与 DI 安装代码不在同一个项目中)并且不知道您想要使用什么类型，搜索程序集的接口和实现是一种非常明智的方法。(即使在这种情况下，我也不会按名称搜索它们，因为神奇的字符串。)MEF 就是针对这种情况而存在的。它叫做*插件架构*。使用插件架构在一个项目中组合几个已知的类是不错的代码。这是邪恶的代码。

所以尽管我最初的反应是，MEF 不是问题。它不应该成为国际奥委会的容器。也许微软 MVP 的这篇博文标题为“MEF 不是 IoC 容器；但 MEF 使用 IoC”也是一种暗示。另一个是“谷歌搜索”。net dependency injection containers”返回了许多包含流行库的列表，如 Autofac、Windsor、Ninject、SimpleInjector 和 Unity，后面是几个我以前从未听说过的库，但没有一个提到 MEF，因为 *MEF 不是 IoC 容器。*

如果你需要选择一个 IoC 容器作为每个人使用的标准，并且正在考虑 MEF，Newtonsoft。JSON，EPPlus，或者任何不是依赖注入容器的东西，请使用我配置的这个工具，它将帮助你选择比 MEF 更合适的东西。

如果我大错特错，并且 MEF 在我们不需要发现类型并且不想在代码中粘贴冗余属性的常见场景中通常用作 IoC 容器，请尽快给我发电子邮件，以免发生什么意外，让这些知识永远消失。

### 被纯迪所救

我如何用 MEF 替换 Windsor(它不是 IoC 容器？)我该如何配置命名依赖项和抽象工厂？

我意识到一些事情:

*   我只处理了大约十几种类型，其中，消费应用程序只需要与单个接口进行交互。
*   我的类是无状态的，所以我不需要担心使用容器来管理对象的生存期。
*   我的抽象工厂的实现看起来比它们需要的更复杂。它们并不坏，但它们需要大约三分钟向不熟悉 Windsor 的人解释，而不是从未见过 Windsor 的人在看到它的那一刻就能理解的其余配置，此时他们会立即理解 80%的如何使用 Windsor 或任何其他容器。我喜欢让人们的事情变得简单，但我也假设他们和我一样聪明能干。如果我知道一些他们不知道的事，也许他们知道另外三件我不知道的事。但是我跑题了。我的抽象工厂不必要的复杂。

出于习惯，我倾向于使用 IoC 容器，因为即使对于数量较少的类，我也更喜欢用它来调用嵌套构造函数。我还认为，在这种情况下，使用 Windsor 进行简单配置将提供一个有用的示例。但是如果我唯一可以使用的 IoC 容器根本不是 IoC 容器，那么更好的选择可能是根本没有容器，也就是纯 DI。(它也被称为“穷人的 DI”，这意味着它总是不受欢迎，IoC 容器不是免费的开源软件，所有的开发人员都是男性。)

记住，依赖注入不使用容器。这是我们写类的方式。类本身不知道它们是使用容器创建的，还是通过“正常”方式调用它们的构造函数创建的。如果他们确实需要容器来运行，那么我们就做错了。穷人的 DI 意味着已经创建了依赖于依赖注入的类，我们用老式的方法创建它们，没有容器。

结果和我习惯的不一样，但总的来说也不算是世界末日。简而言之，应用程序接收一个字节数组中的二进制数据，以及一个指示它接收到的数据类型的字符串。它使用该字符串来确定应该创建哪个`IFoo`实现来处理数据。(这就是抽象工厂的由来。)下面是工厂实现，它取代了我用 Windsor 所做的工作:

```
 public class FooFactory
    {
        private readonly Dictionary<string, IFoo> _foos
            = new Dictionary<string, IFoo>(StringComparer.OrdinalIgnoreCase);

        public FooFactory()
        {
            // shared
            var fooWidget = new FooWidget(
                new WidgetProvider(
                    new WidgetBuilder(),
                    new[]
                    {
                        TypeConversions.AdjustIncompatibleTypes, //injecting delegates
                        TypeConversions.AdjustInvalidStrings     //instead of interfaces
                    }));
            var fooMapper = new FooMapper(new BlargFormatter(), new PaulaBeanFormatter());
            var dataValidator = new SpecializedFormatDataValidator();

            // Red (the same Foo is used for two data types)
            var blueSpecializedFormatReader = new BlueSpecializedFormatReader(dataValidator);
            var redFoo = new RedFoo(
                blueSpecializedFormatReader,
                fooWidget, 
                fooMapper,
                FooDirectories.GetDirectory);
            _foos.Add(FooDataTypes.RedFooOne, redFoo);
            _foos.Add(FooDataTypes.RedFooTwo, redFoo);

            // Blue
            var blueFooReader = new SpecializedFormatReader(dataValidator);
            var blueFoo = new BlueFoo(
                new FooTypeSelector(),
                FooDirectories.GetDirectory,
                blueFooReader,
                fooWidget,
                fooMapper);
            _foos.Add(FooDataTypes.BlueFoo, blueFoo);

            // Green
            var greenFoo = new GreenFoo(fooWidget, fooMapper);
            _foos.Add(FooDataTypes.GreenFoo,
                greenFoo);
        }

        public IFoo GetFoo(string dataType)
        {
            if (_foos.ContainsKey(dataType))
                return _foos[dataType];
            throw new ArgumentException($"No foo is specified for data type {dataType}");
        }
    } 
```

如你所见，我正在创建`IFoo`的各种实现，并把它们放在一个`Dictionary<string, IFoo>`中，这样我就可以调用`GetFoo`在运行时检索我需要的实现。

强调一下，这不是我的第一选择。这些嵌套的构造函数调用看起来像是一个例子，展示了为什么我们使用 IoC 容器而不是这样做。有人有理由感到困惑，想知道为什么我没有。另一方面:

*   虽然 Windsor 或 Autofac 不难理解，但阅读本文并不需要任何人学习任何新知识。(用一个 IoC 容器编写它，以便人们可以看到它并学习一些新的东西，这可能会更好，但你不能赢得所有人。)
*   最长的方法没那么长。如果它变得更长，我可以很容易地把它分解成更小的方法。
*   我最终消除了一些稍微复杂的抽象工厂代码，并将我的类组合与产生`IFoo`实例的工厂结合起来，这意味着总体代码更少。
*   用测试很容易捕捉到阿迪注册中丢失的组件，但是现在如果我丢失了一个构造函数参数，就会立即出现编译器错误。
*   如果我需要包含一些不是在这个工厂中创建的其他依赖项，我可以将它注入到工厂中，然后注入到需要它的类中。
*   我的生产代码和集成测试都使用我的 Windsor 工厂来创建测试主题。现在他们用这个工厂。我不需要做任何改变。
*   我没有通过编写邪恶的、脆弱的代码来搜索我的接口和类，以“找到”我已经知道的类型，从而违反了所有自然和体面的事情。

这种方法很快就会出现问题。例如，如果我需要作用域或者创建不仅仅是`IFoo`的实现怎么办？我不想让上面的代码变得更大更复杂或者有几个这样的“工厂”类。如果我需要创建共享某些依赖关系的不相关的类实例呢？IoC 容器通过一次创建一个依赖项并在需要的地方重用它，使这变得更容易。但是现在我已经证明了我的代码与我用来构造类实例的任何方法都是分离的，所以我可以根据需要再把它改回来。有人甚至可以弯曲时间和空间来使用 MEF，如果他们想的话。

尽管如此，我还是陷入了总是以同样的方式做事的舒适的窠臼，所以奇怪的是，意识到我的纯 DI 版本在某些方面比我的 Windsor 版本更简单、更好，这是一种解放。带走一样东西，看到没有它也能活下去，真好。