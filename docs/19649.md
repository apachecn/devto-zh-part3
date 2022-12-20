# 处理遗留代码的技术

> 原文：<https://dev.to/petermorlion/techniques-for-tackling-legacy-code-56h8>

遗留代码通常是没人喜欢接触的代码。它通常很复杂，违反各种最佳实践，很少或没有测试，等等。

然而，我发现在遗留代码中工作是非常有意义的。这需要一段时间，但当你开始驯服这头野兽，并了解它的复杂性，它实际上可以很有趣！

但是，如何开始改变一个遗留项目，加入现代标准，并使其可维护呢？本文将根据我的经验解释一些技巧。我不会以专家自居，但我在职业生涯中见过一些遗留项目，并且几乎总是喜欢参与其中。另外，我似乎总是用同样的技巧来解决它们。不过有一些先决条件，我稍后会谈到。

## 依赖注入

我首先推荐的是添加依赖注入。这听起来像是遗留应用程序中的一个大工程，但是您不需要一次完成所有这些。第一步是识别应用程序的入口点，并在那里初始化一个空容器。

您的应用程序可能包含一个或多个类，然后创建它们的依赖关系的实例。在 ASP.NET 应用程序中，这些将是你的控制器。在 WPF 应用程序中，您的主视图或视图模型。取其中一个依赖项，将其注册到您的 DI 容器中，并使用您的容器实例化依赖于它的类(也应该在容器中注册)。

以这个虚构的控制器为例:

```
public class CustomerController : ApiController
{
    public CustomerController() {}

    public IHttpActionResult Get()
    {
        var logger = new Logger();
        logger.Info("Retrieving all customers");

        var repository = new CustomerRepository();
        var customers = repository.GetAll();
        return customers;
    }
} 
```

CustomerRepository 类可能会访问数据库，这使得测试变得很困难。我们可以很容易地将其改为:

```
public class CustomerController : ApiController
{
    private CustomerRepository _repository;

    public CustomerController(CustomerRepository repository) 
    { 
       _repository = repository;
    }

    public IHttpActionResult Get()
    { 
        var logger = new Logger(); 
        logger.Info("Retrieving all customers");

        var customers = _repository.GetAll();
        return customers;
    }
} 
```

现在，所有其他依赖项仍将由它们的依赖项创建，但至少您现在有了一条添加越来越多的依赖项的道路。似乎什么都没有真正改变。但这是将您的应用程序分解成更小的构建块的第一步，这样您可以更容易地管理和测试，并且您可以根据自己的意愿进行组合。

有些情况会带来更大的挑战。但是这些也是可以处理的。

### 多个实例

如果一个类依赖于另一个类，但是它创建了它的多个实例，那该怎么办？您不能将它注入到构造函数中，因为那样您将只有一个实例。看看这段创建多个视图模型并将其添加到 ObservableCollection 的 WPF 代码:

```
public void LoadCustomers()
{
    var customers = _repository.GetAll();
    foreach (var customer in customers)
    {
        var customerViewModel = new CustomerViewModel(customer);
        CustomerViewModels.Add(customerViewModel);
    }
} 
```

一个解决办法是注入一个工厂。然后，依赖类可以多次调用(注入的)工厂，而不是创建依赖本身的多个实例:

```
public void CustomerOverviewViewModel(CustomerViewModelFactory factory)
{
    _factory = factory;
}

public void LoadCustomers()
{
    var customers = _repository.GetAll();
    foreach (var customer in customers)
    {
        var customerViewModel = _factory.Create(customer);
        CustomerViewModels.Add(customerViewModel);
    }
} 
```

如果你正在使用 Autofac，你不需要写你自己的工厂类。Autofac 有一个内置的[委托工厂](http://autofaccn.readthedocs.io/en/latest/advanced/delegate-factories.html)的概念来提供这个功能。

### 构造函数参数

如果依赖关系是用一些参数构造的呢？好吧，如果是另一个可以在容器中注册的类，你的 DI 框架应该可以处理。但是如果在构造时它是一个局部变量，上面解释的工厂技术可以再次使用。事实上，在我们上面的例子中，我将不同的客户传递给每个新的 CustomerViewModel。

### 其他特殊情况

您可能会遇到其他特殊情况，需要以添加更多遗留代码的方式来解决。如果没有其他选择，那也行。将一个传统应用程序转变成一个更现代、更精益的动物是一项长期的努力，有时需要你在事情变好之前让事情变得更糟。纪律是这里的关键，所以你记得以后要改进它。

## 组件化

另一个重要的技巧是开始将那些大类分解成更小的类。

第一步可以是将庞大的方法分割成较小的逻辑部分。通常，您可以通过代码块上面的注释来确定什么应该是方法:

```
public void UpdatePoints(Player player)
{   
    // Calculate general multiplier based on special items in inventory
    var generalMultiplier = 1;
    var specialItems = player.Inventory.Where(x => x.IsSpecialItem).ToList();
    foreach (var specialItem in specialItems)
    {
        // Now for some complex logic where certain items add to the multiplier,
        // but some items can also block the abilities of other items.
    }

    // Add points based on items
    var inventoryScore = 0;
    foreach (var item in player.Inventory)
    {
        // Add to skill points based on characteristics of items in inventory
    }

    // And so on...

    // Now set the definite points using all calculated values above
} 
```

不使用注释，为什么不使用带有描述性名称的方法？这个比较好读:

```
public void UpdatePoints(Player player)
{   
    var generalMultiplier = CalculateGeneralMultiplier(player);
    var inventoryScore = CalculateInventoryScore(player);
    var achievementsScore = CalculateAchievementsScore(player);
    // And so on...

    var result = (inventoryScore + achievementsScore) * generalMultiplier;
} 
```

这样会让大家的流程更清晰。这也将使它更易于维护。事情如何完成的细节隐藏在私有方法中。如果你需要它们，它们是可用的，但是如果你只需要理解一个方法在做什么，这个方法就足够了。

稍后，如果代码属于同一个类(内聚性，请记住)，您可以将这些方法提取到单独的类中。您最终会得到许多小的构建块，它们可以更容易地混合和匹配，也可以重构、更改和测试。

这些构件将非常适合我们的 DI 容器！

## 测试

当您将这些大型类分开时，请抓住机会为这些较小的代码块编写测试。

测试将会比以前更容易编写，添加更多的测试将会给你更大的信心沿着这条路走下去。

在遗留代码库中，编写一个广泛的测试套件是很重要的。遗留代码库的主要特征之一是缺少测试。而且有测试的时候，开发者通常也不够信任。要么是因为测试不多，要么是因为写的很差。测试没有给开发人员足够的信心来重构代码或添加功能。

向遗留应用程序添加越来越多的测试将迫使您将大块代码组件化和解耦，从而提高代码的质量和架构。这反过来会使编写测试更加容易。这是一个良性循环，给你足够的时间，它会把你从之前的停滞状态中拉出来。

## 编码标准

遗留项目中经常发生的事情是多种风格的编程混合在一起。这在架构基础上是正确的([熔岩层反模式](http://mikehadlow.blogspot.com/2014/12/the-lava-layer-anti-pattern.html))，但在纯粹的编码风格层面上也是如此。虽然大多数聪明的开发人员可以看到这一点，但他们也会对此感到沮丧。在一个遗留项目中，已经有足够多的挫折了。

如果应用程序有一致的风格，我不会试图立即改变它。即使是我不喜欢的风格。一个遗留项目有其他需要解决的问题，所以如果有一个一致的风格，至少有那个。

但如果是风格混搭的话，就该走什么路线达成一致是个好主意。实施每个人都同意的编码标准。不要进行过多的讨论。实际上有一个标准比标准里有什么更重要。制表符还是空格？一个不比另一个好。就选一个，坚持下去。

您的遗留应用程序可能会在数百处(如果不是数千处)打破标准。不要担心那个。只需应用童子军规则，逐一解决违规问题。

## 本地运行

当您开始开发一个新的应用程序时，您通常可以在本地运行它。许多应用程序在这个过程中失去了这种功能。然而，其重要性怎么强调都不为过。能够在本地运行应用程序允许您在不打扰任何人的情况下进行试验和开发。

本地跑就是本地跑。这可能看起来很傻，但是这也意味着在本地运行数据库。这通常是遗留应用程序的问题。

这是我在开发遗留应用程序时首先投入时间的事情之一。不要低估当你突然可以孤立地运行应用程序时所获得的自由。你现在可以在没有互联网连接的情况下，尝试新事物、运行负载测试、开发(通勤者？)，等等。

## 前提条件

在你开始做以上所有事情之前，有一些先决条件。这些通常是组织性的，即管理层应该同意你投入时间(和他们的钱)，应该允许错误发生，等等。但有些也是技术性的，比如定期备份、良好的源代码控制等。我会在下一篇的[文章中谈到这些。](https://www.petermorlion.com/techniques-for-tackling-legacy-code-parte-deux/)