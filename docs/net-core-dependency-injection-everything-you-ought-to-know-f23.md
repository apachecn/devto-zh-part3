# 。NET 核心依赖注入:你应该知道的一切

> 原文：<https://dev.to/jamesmh/net-core-dependency-injection-everything-you-ought-to-know-f23>

如果你去看 ASP.NET 核心的官方文档，你会发现依赖注入在“基础”部分。

这是真的吗——这真的是基本原则吗？

*注:这篇文章是 [2018 C#降临节日历](https://crosscuttingconcerns.com/The-Second-Annual-C-Advent)的一部分。*

最初发表于[我的博客](https://www.blog.jamesmichaelhickey.com/NET-Core-Dependency-Injection/)。

# TLDR；

依赖注入被嵌入到。网芯。而且，它的存在是有原因的:它通常促进最佳编码实践，并为开发人员提供构建可维护、模块化和可测试软件的工具。

它还为库作者提供了一些工具，可以帮助他们非常简单明了地安装/配置库。

# 我从构建 Coravel 中学到了什么:第三部分

这是正在进行的系列的第 3 部分。其他版本包括:

*   第 1 部分:到目前为止我所学到的构建 Coravel(开源。净芯工装)
*   第 2 部分:流畅的 API 让开发人员喜欢使用您的。NET Libraries(builtwithdot.net 客座博文)

正如你所猜测的，这篇文章将回顾一些我在中了解到的关于 DI 的事情。NET Core，以及我对您应该了解的内容的建议。😊

首先，我想为那些可能不太熟悉依赖注入的人探索一下 DI。我们将从基础开始，然后转向一些更高级的场景。

如果你已经知道什么是 DI，以及如何使用接口来模拟和测试你的类，等等。然后您可以在中使用依赖注入移到[上。网芯部分](#Using-Dependency-Injection-In-NET-Core)。

是的，这将是一个漫长的过程。准备好。😎

# 什么是依赖注入？

如果你不熟悉 DI，它只是把依赖关系作为一个外部参数传递给你的对象。

这可以通过对象的构造函数或方法来完成。

```
// Argument "dep" is "injected" as a dependency.
public MyClass(ExternalDependency dep)
{
    this._dep = dep;
} 
```

Enter fullscreen mode Exit fullscreen mode

从根本上说，依赖注入只是将依赖作为参数传递。就是这样。仅此而已。

良好的...如果那真的是 DI 的全部，我就不会写这个了。😜

# 为什么要传递依赖关系作为自变量？

你为什么想这么做？几个原因:

*   促进将逻辑拆分成多个更小的类和/或结构
*   提高代码的可测试性
*   提倡使用抽象，这通常允许更模块化的代码结构

让我们简单地看一下这促进了可测试性的想法(这反过来影响了提到的所有其他点)。

我们为什么要测试代码？确保我们的系统运行正常。

这意味着**你可以信任你的代码**。

没有测试，**你不能真正信任你的代码**。

我在另一篇关于重构遗留系统的博文中对此进行了更详细的讨论。

# 什么是依赖注入(再探)

当然，DI 不仅仅是“传递参数”依赖注入是一种机制。NET Core)会自动将所需的依赖项传递(注入)到类中。

我们为什么需要它？

看看这个简单的类:

```
public class Car
{
    public Car(Engine engine)
    {
        this._engine = engine;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果在其他地方，我们需要这样做呢:

```
Car ferrari = new Car(new Engine()); 
```

Enter fullscreen mode Exit fullscreen mode

太好了。如果我们想测试这个`Car`类呢？

问题是**为了测试`Car`你需要`Engine`。**如果你愿意的话，这是一种“硬”依赖。

这意味着这些类紧密地联系在一起。换句话说，紧密耦合。那些是不好的词。

我们想要松散耦合的类。这使得我们的代码更加模块化、通用化并且更容易测试(这意味着**更多的信任**和**更多的灵活性**)。

# 快速查看测试

测试时的一些常用技术是使用“模拟”。mock 只是一个“假装”是真实实现的存根类。

我们不能模仿具体的类。但是，我们可以模仿接口！

让我们将我们的`Car`改为依赖于一个接口:

```
public class Car
{
    public Car(IEngine engine)
    {
        this._engine = engine;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

酷！让我们来测试一下:

```
// Mock code configuration would be here.
// "mockEngine" is just a stubbed IEngine.

Car ferrari = new Car(mockEngine);

Assert.IsTrue(ferrari.IsFast()); 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们正在测试`Car`类**，它不依赖于`Engine`和**。👍

# 快速浏览模块化

我提到过使用 DI 可以让你的代码模块化。其实，并不是 DI，而是上面的技术(依赖于接口)。

比较这两个例子:

```
Car ferrari = new Car(new FastEngine()); 
```

Enter fullscreen mode Exit fullscreen mode

和

```
Car civic = new Car(new HondaEngine()); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们依赖于界面，所以在制造什么样的汽车方面，我们有更多的灵活性！

# 避免类继承

另一个好处是你不需要使用类继承。

这是我经常看到的被滥用的东西。以至于我尽我所能“从不”使用类继承。

这很难测试，很难理解，而且通常会导致构建一个不正确的模型，因为事后很难改变。

99%的时候都有更好的方法来使用这样的模式构建代码——这依赖于抽象而不是紧密耦合的类。

是的，类继承是代码中最高度耦合的关系！(但那是另一篇博文了😉)

# 使用依赖注入。网络核心

上面的例子强调了我们为什么需要 DI。

依赖注入允许我们“绑定”一个全局使用的特定类型来代替，例如，一个特定的接口。

在运行时，我们依靠 DI 系统为我们创建这些对象的新实例。所有的依赖关系都是自动处理的。

英寸 NET Core 中，你可能会做这样的事情来告诉 DI 系统在请求某些接口时我们想要使用什么类，等等。

```
// Whenever the type 'Car' is asked for we get a new instance of the 'Car' type.
services.AddTransient<Car, Car>(); 
// Whenever the type 'IEngine' is asked for we get a new instance of the concrete 'HondaEngine' type.
services.AddTransient<IEngine, HondaEngine>(); 
```

Enter fullscreen mode Exit fullscreen mode

`Car`依靠`IEngine`。

当 DI 系统试图“构建”(实例化)一个新的`Car`时，它将首先获取一个`new HondaEngine()`，然后将其注入到`new Car()`。

每当我们需要一个`Car`。NET Core 的 DI 系统会自动为我们安装！所有依赖项将级联。

因此，在一个 MVC 控制器中，我们可以这样做:

```
public CarController(Car car)
{
    this._car = car; // Car will already be instantiated by the DI system using the 'HondaEngine' as the engine.
} 
```

Enter fullscreen mode Exit fullscreen mode

# 真实世界的例子

好吧，汽车的例子很简单。那就是把基础的东西弄下来。让我们看一个更现实的场景。

准备好。😎

我们有一个在应用程序中创建新用户的用例:

```
public class CreateUser
{    
    // Filled out later...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个用例需要发出一些数据库查询来持久化新用户。

为了使这一点可测试——并确保我们可以测试我们的代码**而不需要数据库作为依赖**——我们可以使用已经讨论过的技术:

```
public interface IUserRepository
{
    public Task<int> CreateUserAsync(UserModel user);
} 
```

Enter fullscreen mode Exit fullscreen mode

而具体实现会打到数据库:

```
public class UserRepository : IUserRepository
{
    private readonly ApplicationDbContext _dbContext;

    public UserRepository(ApplicationDbContext dbContext) =>
        this._dbContext = dbContext;

    public async Task<int> CreateUserAsync(UserModel user)
    {
        this._dbContext.Users.Add(user);
        await this._dbContext.SaveChangesAsync();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 DI，我们会得到这样的结果:

```
services.AddTransient<CreateUser, CreateUser>();
services.AddTransient<IUserRepository, UserRepository>(); 
```

Enter fullscreen mode Exit fullscreen mode

每当我们有一个需要`IUserRepository`实例的类时，DI 系统会自动为我们构建一个`new` `UserRepository`。

对于`CreateUser`来说也是如此——当我们被要求时，一个新的`CreateUser`将会被给我们(连同它所有的依赖项已经被注入)。

现在，在我们的用例中，我们这样做:

```
public class CreateUser
{
    private readonly IUserRepository _repo;

    public CreateUser(IUserRepository repo) =>
        this._repo = repo;

    public async Task InvokeAsync(UserModel user)
    {
        await this._repo.CreateUserAsync(user);
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

在一个 MVC 控制器中，我们可以“要求”一个`CreateUser`用例:

```
public class CreateUserController : Controller
{
    private readonly CreateUser _createUser;

    public CreateUserController(CreateUser createUser) =>
        this._createUser = createUser;

    [HttpPost]
    public async Task<ActionResult> Create(UserModel userModel)
    {
        await this._createUser.InvokeAsync(userModel);
        return Ok();
    }    
} 
```

Enter fullscreen mode Exit fullscreen mode

DI 系统将自动:

*   尝试创建一个新的`CreateUser`实例。
*   由于`CreateUser`依赖于`IUserRepository`接口，DI 系统接下来将查看是否有类型“绑定”到该接口。
*   是的——是混凝土`UserRepository`。
*   创建新的`UserRepository`。
*   将它传递到一个新的`CreateUser`中，作为它的构造函数参数`IUserRepository`的实现。

一些显而易见的好处:

*   您的代码更加模块化和灵活(如前所述)
*   你的控制器等等。(无论使用 DI 的是什么)变得更加简单易读。

# 真实世界测试

最后一个好处是，我们可以测试这个**，而不需要访问数据库**。

```
// Some mock configuration...
var createUser = new CreateUser(mockUserRepositoryThatReturnsMockData);
int createdUserId = await createUser.InvokeAsync(dummyUserModel);

Assert.IsTrue(createdUserId == expectedCreatedUserId); 
```

Enter fullscreen mode Exit fullscreen mode

这有助于:

*   快速测试(无数据库)
*   隔离测试(只关注测试`CreateUser`中的代码)

# 关于使用你应该知道的。净核心依赖注入

现在我想介绍一些你应该知道的更恰当的技术术语，以及推荐的相关知识。NET Core 的 DI 系统。

## 服务商

当我们提到“DI 系统”时，我们实际上是在谈论服务提供商。

在其他框架或 DI 系统中，这也被称为*服务容器*。

这是保存所有 DI 内容的配置的对象。

它也是最终将被“要求”为我们创造新对象的东西。因此，它决定了每个服务在运行时需要什么依赖关系。

## 装订

当我们谈论绑定时，我们只是指类型`A`被映射到类型`B`。

在我们关于`Car`场景的例子中，我们会说`IEngine`被绑定到`HondaEngine`。

当我们请求一个`IEngine`的依赖项时，我们返回一个`HondaEngine`的实例。

## 解析

解析是指找出特定服务需要哪些依赖关系的过程。

使用上面的`CreateUser`用例的例子，当服务提供者被要求注入一个`CreateUser`的实例时，我们会说提供者正在“解决”这个依赖。

解析包括找出整个依赖关系树:

*   `CreateUser`需要一个`IUserRepository`的实例
*   提供者看到`IUserRepository`被绑定到`UserRepository`
*   `UserRepository`需要一个`ApplicationDbContext`的实例
*   提供者看到`ApplicationDbContext`是可用的(并且绑定到相同的类型)。

找出级联依赖的树就是我们所说的“解析服务”

## 范围

通常称为作用域，或者称为服务生存期，这指的是服务是短期的还是长期的。

例如，singleton(正如模式所定义的)是一种每次都会解析到同一个实例的服务。

如果不了解什么是作用域，你可能会遇到一些非常奇怪的错误。😜

的。NET Core DI 系统有 3 个不同的范围:

### 单胎

```
services.AddSingleton<IAlwaysExist, IAlwaysExist>(); 
```

Enter fullscreen mode Exit fullscreen mode

例如，每当我们在 MVC 控制器构造函数中解析`IAlwaysExist`时，它总是完全相同的实例。

顺便提一下:这意味着对线程安全等的关注。这取决于你在做什么。

### 作用域

```
services.AddScoped<IAmSharedPerRequests, IAmSharedPerRequests>(); 
```

Enter fullscreen mode Exit fullscreen mode

作用域是最复杂的生存期。我们稍后将更详细地研究它。

简单地说，这意味着在一个特定的 HttpRequest(在 ASP。NET 核心应用程序)解析的实例将是相同的。

假设我们有服务`A`和`B`。两者由同一个控制者解决:

```
public SomeController(A a, B b)
{
    this._a = a;
    this._b = b;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在想象一下`A`和`B`都依赖于服务`C`。

如果`C`是一个作用域服务，并且由于作用域服务为同一个 HTTP 请求解析到同一个实例，所以`A`和`B`都将注入完全相同的`C`实例。

然而，对于所有其他 HTTP 请求，将实例化一个不同的`C`。

### 瞬变

```
services.AddTransient<IAmAlwaysADifferentInstance, IAmAlwaysADifferentInstance>(); 
```

Enter fullscreen mode Exit fullscreen mode

瞬态服务在解析时总是一个全新的实例。

举这个例子:

```
public SomeController(A a, A anotherA)
{
} 
```

Enter fullscreen mode Exit fullscreen mode

假设类型`A`被配置为瞬态服务，变量`a`和`anotherA`将是类型`A`的不同实例。

*注意:举一个相同的例子，如果`A`是一个限定了作用域的服务，那么变量`a`和`anotherA`将是同一个实例。然而，在下一个 HTTP 请求中，如果`A`被限定了作用域，那么下一个请求中的`a`和`anotherA`将与第一个请求中的实例不同。*

*如果`A`是单例的，那么**中的变量`a`和`anotherA`都会引用同一个单例。***

## 范围问题

当使用试图相互依赖的不同作用域的服务时，会出现一些问题。

### 循环依赖

就是不做。这说不通😜

```
public class A
{
    public A(B b) { }
}

public class B
{
    public B(A a){ }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 单例+可传递服务

单身者又一次“永远”活着。总是同一个例子。

另一方面，可传递服务在被请求或解决时总是一个不同的实例。

所以这里有一个有趣的问题:当一个单体依赖于一个可传递的依赖关系**时，这个可传递的依赖关系存在多久？**

答案是**永远**。**更确切地说，只要它的父母活着。**

因为单例对象永远存在，所以它引用的所有子对象也将永远存在。

这不一定是坏的。但是当您不理解这个设置意味着什么时，它可能会引入奇怪的问题。

#### 线程安全

也许你有一个可传递的服务——姑且称之为`ListService`它不是线程安全的。

`ListService`有一个内容列表，并向这些项目的`Add`和`Remove`公开方法。

现在，你开始在单例中使用`ListService`作为依赖。

该单例将在任何地方被重用。这意味着，在每个 HTTP 请求上的**。这意味着**在许多许多不同的线程上。****

 **因为单例访问/使用`ListService`，并且`ListService`不是线程安全的——大问题！

小心点。

### 单例+范围服务

现在让我们假设`ListService`是一个作用域服务。

如果你试图将一个限定了作用域的服务注入到一个单体中，会发生什么呢？

**。网芯会炸，告诉你不行！**

还记得作用域服务和 HTTP 请求一样长吗？

但是，还记得我说过实际上比这更复杂吗？...

#### 作用域服务是如何工作的

在被子下面。NET Core 的服务提供者公开了一个方法`CreateScope`。

*注:或者，您可以使用`IServiceScopeFactory`并使用相同的方法`CreateScope`。我们稍后再来看这个*😉

`CreateScope`创建一个实现`IDisposable`接口的“作用域”。它会被这样使用:

```
using(var scope = serviceProvider.CreateScope())
{
    // Do stuff...
} 
```

Enter fullscreen mode Exit fullscreen mode

服务提供者还公开了解析服务的方法:`GetService`和`GetRequiredService`。

它们之间的区别在于，当服务没有绑定到提供者时，`GetService`返回 null，而`GetRequiredService`将抛出一个异常。

因此，作用域可以这样使用:

```
using(var scope = serviceProvider.CreateScope())
{
    var provider = scope.ServiceProvider;
    var resolvedService = provider.GetRequiredService(someType);
    // Use resolvedService...
} 
```

Enter fullscreen mode Exit fullscreen mode

什么时候。NET Core 在后台开始一个 HTTP 请求，它会做类似的事情。例如，它将解析您的控制器可能需要的服务，因此您不必担心底层细节。

就将服务注入 ASP 控制器而言，范围服务基本上附加在 HTTP 请求的生命周期中。

但是，我们可以创建自己的服务(这将是服务定位器模式的一种形式——稍后将详细介绍)！

因此，作用域服务仅**附加到 HTTP 请求是不正确的。其他类型的应用程序可以在它们需要的任何生命周期或上下文中创建自己的作用域。**

#### 多个服务商

注意每个作用域是如何拥有自己的`ServiceProvider`？那是怎么回事？

DI 系统有**多个服务提供商。哇哦🤯**

单例是从根服务提供者(在应用程序的生命周期内存在)解析的。根提供者**未在**的范围内。

每当你创建一个作用域，你就得到一个新的**作用域的**服务提供者！这个作用域提供者仍然能够解析单体服务，但是通过代理，它们来自根提供者，因为所有作用域提供者都可以访问它们的“父”提供者。

下面是我们刚刚学到的内容的概要:

*   单体服务总是可解析的(从根提供者或通过代理)
*   可传递服务总是可解析的(从根提供者或通过代理)
*   作用域服务需要作用域，因此需要可用的作用域服务提供者

那么，当我们试图从根提供者(非作用域提供者)解析作用域服务时会发生什么呢？...

繁荣🔥

#### 回到我们的话题

也就是说，作用域服务**需要作用域存在**。

根提供程序解析单例。

由于根提供者没有作用域(从某种意义上来说，它是一个“全局”提供者)，所以将作用域服务注入单例是没有意义的。

### 作用域+传递服务

依赖于可传递服务的作用域服务呢？

实际上，这是可行的。但是，由于与在单例中使用可传递服务相同的原因，它可能不会像您预期的那样运行。

作用域服务所使用的可传递服务将与作用域服务的寿命一样长。

请确保这在您的用例中有意义。

## 对库作者的依赖注入

作为库作者，我们有时希望提供类似本机的工具。例如，对于 Coravel，我想让库与。网芯 DI 系统。

我们如何做到这一点？

### IServiceScopeFactory

顺便提一下。NET Core 提供了一个创建作用域的工具。这对库作者很有用。

库作者可能应该使用`IServiceScopeFactory`，而不是获取`IServiceProvider`的实例。

为什么？嗯，还记得根服务提供者是如何无法解析作用域服务的吗？如果您的库需要围绕作用域服务做一些“魔术”该怎么办？哎呀！

例如，在某些情况下，Coravel 需要从服务提供者那里解析某些类型(比如实例化[可调用类](https://github.com/jamesmh/coravel/blob/master/Docs/Invocables.md))。

实体框架核心上下文是有范围的，所以在库内执行数据库查询(代表用户/开发人员)可能是您想要做的事情。

这就是 Coravel Pro 所做的事情——自动在后台执行来自用户 EF 核心上下文的查询。

顺便提一下，在后台`Task`捕获闭包中的服务和/或从后台`Task`解析服务的问题也促进了手动解析服务的需求(这是 [Coravel](https://github.com/jamesmh/coravel) 需要做的)。

如果感兴趣的话，大卫·福勒已经在这里简单地写了一些。

### 服务定位器模式

一般来说，服务定位器模式不是一个好的实践。这是当我们手动向服务提供商请求特定类型时。

```
using(var scope = serviceProvider.CreateScope())
{
    var provider = scope.ServiceProvider;
    var resolvedService = provider.GetRequiredService(someType);
    // Use resolvedService...
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，对于上面提到的情况，这就是我们需要做的——获取一个范围，解析服务并做一些“魔术”。

这类似于。NET Core 为您的 ASP 准备阿迪范围并解析服务。网络核心控制器。

这并不坏，因为它不是“用户代码”，而是“框架代码”——如果你愿意的话。

# 结论

我们研究了依赖注入为什么是我们可以使用的有用工具的一些原因。

它有助于促进:

*   代码可测试性
*   通过组合实现代码重用
*   代码可读性

然后我们看了依赖注入是如何？NET Core，以及它如何工作的一些底层方面。

总的来说，我们发现当服务依赖于生命周期较短的其他服务时，问题就会出现。

*   单一->作用域
*   单一->传递
*   范围->可传递

最后，我们看了如何。NET Core 为库作者提供了一些有用的工具，有助于与。NET Core 的 DI 系统无缝。

我希望你学到了新东西！像往常一样，在评论中留下你的一些想法👌

* * *

## 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！

## 你可能会喜欢

*   [什么使。网芯这么特别？](https://www.blog.jamesmichaelhickey.com/What-Makes-NET-Core-So-Special-Why-You-Should-Use-NET-Core/)
*   到目前为止我所学到的。净芯工装)
*   流畅的 API 让开发者喜欢使用你的。网络库**