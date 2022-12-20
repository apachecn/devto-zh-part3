# 我将业务规则和验证放在哪里？

> 原文：<https://dev.to/jamesmh/where-do-i-put-my-business-rules-and-validation-o2f>

[*这篇文章最初出现在 builtwithdot.net 的博客上。*T3】](https://builtwithdot.net/blog/where-do-i-put-my-business-rules-and-validation)

曾经为应用程序的业务规则和验证的位置而苦恼过吗？

你应该把它们放在你的 MVC 模型中吗？

应该为验证创建特殊的类吗？

为了回答这个问题，我将向您展示一种在领域驱动设计中使用的久经考验的技术！

# 一本简短的 DDD 入门书

领域驱动设计是设计软件的整体方法和框架。它非常强调从商业问题入手探索商业问题，包括人们目前如何处理这个问题，找出谁是这个领域的专家，向专家咨询，探索建模问题的潜在方法等。

DDD 是一个巨大的主题。今天，我们将关注一项来自 DDD 的技术。

让我们来看两个 DDD 的概念来奠定基础。

## 实体

一个实体仅仅是一个由一些唯一的标识符标识的模型。可能是客户、订单、保险单等。

当测试两个实体是否相同或相等时，我们测试每个实体的惟一 id，看它们是否匹配。

## 值对象

值对象对应于不可唯一识别的事物。像地址、人名或日期范围之类的东西都可以是值对象。

它们可以保存多段数据，比如前面提到的日期范围(有开始和结束日期)。

当测试两个值对象是否相同或相等时，我们测试是否所有的值都相同。

## 两者结合

实体和值对象通常是密切相关的。

一个实体通常由(a)其他实体和(b)值对象组成。

例如，一个订单实体可能包含对几个 OrderItem 实体的引用。但是它也可能包含对购买价格的引用(值对象)。

# 我的验证去哪了？

在 DDD，业务规则和验证进入值对象是一个很好的实践。

有两个基本属性使它成为一项伟大的技术——不变性和在创建时立即验证。

## 立即验证

通过在创建时测试一个值对象是否有效，我们确保了不可能有一个值对象处于无效状态。

## 永恒性

使值对象不可变意味着一旦它们被创建就不能被修改。

这两个属性都确保您不能拥有处于无效状态的对象——无论是通过用无效数据创建它，还是通过导致改变数据的副作用。

知道你总是有有效对象的实例是非常简单而强大的。

# 保险单场景

让我们看一个真实世界的例子，看看如何使用值对象来执行业务验证。

假设我们有验证保险单的需求。该政策具有:

*   被保险人年龄
*   被保险人性别
*   被保险人地址
*   开始日期

让我们假设存在以下规则:

*   基本价格为每月 15 美元
*   当年龄超过 50 岁时，价格翻倍
*   当性别是男性时，价格会翻倍(因为男性更容易受伤)...)
*   如果地址在加拿大，则开始日期必须是当月的月初

## 造型技巧

有一个完整的研究领域围绕着实体和价值对象建模。最终，一般规则是将一起更改的数据保存在同一个地方。

这实际上是一个基本的面向对象原则。一个我们经常选择忽略的问题...

因此，我们需要根据业务需求，弄清楚哪些数据会一起改变:

*   年龄和性别影响价格
*   地址影响开始日期

我们可以通过跟踪哪些数据一起变化来了解我们的模型可能是什么样子:

```
public class Object1
{
  public int Age { get; private set; }
  public Gender Gender { get; private set; }
  public decimal Price { get; private set; }

  public Object1(int age, Gender gender)
  {
    this.Age = age;
    this.Gender = gender;
    this.Price = 15.00m; // 1st business requirement: Base price is $15/month
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class Object2
{
  public Address Address { get; private set; }
  public DateTime DateStarted { get; private set; }

  public Object2(Address address, DateTime dateStarted)
  {
    this.Address = address;
    this.DateStarted = dateStarted;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 关于命名的说明

当使用这种技术创建对象时，通常建议不要马上命名值对象和实体。

这确保您关注数据以及基于业务的事物归属。在我们自己的头脑中给我们的对象贴上标签实在是太诱人了——这就导致了对每个模型中“应该”属于什么的偏见。

但是我们不应该这样决定——业务需求应该。一旦我们确信我们的模型和我们能做的一样好，我们就可以给它们起一个有意义的名字。

## 添加我们的业务规则

让我们添加我们的前 3 条规则。

我们将在对象的构造函数中添加这种验证。这满足了需要在创建时有效的值对象的属性。您应该永远不能创建保存无效数据的对象。

```
public class Object1
{
  public int Age { get; private set; }
  public Gender Gender { get; private set; }
  public decimal Price { get; private set; }

  public Object1(int age, Gender gender)
  {
    // Rule #1: Base price is $15/month.
    decimal basePrice = 15.00m;

    // Rule #2: When age is above 50 then price is doubled.
    if(age >= 50)
    {
      basePrice = basePrice * 2;
    }

    // Rule #3: When gender is Male then price is doubled.
    if(gender == Gender.Male)
    {
      basePrice = basePrice * 2;
    }

    this.Age = age;
    this.Gender = gender;
    this.Price = basePrice;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 如何处理无效状态

我们将使用一种技术来确保我们的值对象永远不会处于无效状态:

*   在值对象的构造函数中测试业务需求
*   如果规则失败，抛出异常

为此，我们将创建一个新的异常类型:

```
public class BusinessRuleException : Exception 
{
  public BusinessRuleException(string message) : base(message) { }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用它来添加下一个业务规则:

```
public class Object2
{
  public Address Address { get; private set; }
  public DateTime DateStarted { get; private set; }

  public Object2(Address address, DateTime dateStarted)
  {
    // Rule #4: If address is in Canada then Date Started must be the beginning of the current month.
    if(address.IsCanadian && dateStarted.Day != 1)
    {
      throw new BusinessRuleException("Canadian policies must begin on the first day of the current month.");
    }

    this.Address = address;
    this.DateStarted = dateStarted;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有人可能会指出，我们可以将开始日期自动更改为月初。

但是，这是要求的一部分。我们是否为用户转换日期？在这种情况下，我们的业务只是想通知用户这个业务规则。

再往上，调用者会捕捉异常并向用户显示错误消息。

## 但是显示多个错误信息怎么办？

很高兴你问了！

总的来说，一种方法是用静态工厂方法替换构造函数。您可能会返回一个元组(在 C#中)作为结果:

```
public class Object2
{
  public Address Address { get; private set; }
  public DateTime DateStarted { get; private set; }

  // Make sure no one can create this object with a constructor.
  private Object2() { }

  // Static Factory Method that returns (a) the value object and (b) any errors.
  public static (Object2, IEnumerable<string>) Make(Address address, DateTime dateStarted)
  {
    var errors = new List<string>();

    // Rule #4: If address is in Canada then Date Started must be the beginning of the current month.
    if(address.IsCanadian && dateStarted.Day != 1)
    {
      errors.Add("Canadian policies must begin on the first day of the current month.");
    }

    // Imagine that there might be more business rules here.

    if(errors.Any()){
      return (null, errors); // Never return an invalid instance of the value object ;)
    }

    return (
      new Object2
      {
        Address = address,
        DateStarted = dateStarted
      }, 
      errors
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

还有其他模式可以做到这一点，比如结果对象模式。

## 建筑我们的实体

现在，让我们命名我们的价值对象，并为我们的总体保险单创建一个实体。

注意:我的名字不是最好的。给这些有意义的名字，其实需要很长的时间和思考。如果你的业务充分利用了 DDD，那么提前查看一种无处不在的语言将会告诉你选择使用什么名字，并潜在地告诉你如何选择你的对象的模型。

```
public class InsurancePolicy
{
  public PolicyPricing Pricing { get; private set; }
  public PolicyAddress Address { get; private set; }

  public InsurancePolicy(PolicyPricing pricing, PolicyAddress address)
  {
    this.Pricing = pricing;
    this.Address = address;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用我们的值对象进行验证

下面是我们如何提供用户输入并创建我们的实体:

```
try {
  var pricing = new PolicyPricing(age, gender);
  var address = new PolicyAddress(userAddress, dateStarted);

  // You can never get here unless all business rules are valid.
  var entity = new InsurancePolicy(pricing, address);

  // Now you might save the entity in a DB?
}
catch(BusinessRuleException ex)
{
  errorMessage = ex.Message; // This would be returned to the UI, etc.
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

您可能会注意到，测试您的值对象可以使测试特定的业务规则变得非常容易！

# 出发！

我希望这是一个有帮助的介绍，告诉你如何在你的代码中结合这个领域驱动的设计概念。

以这种方式使用值对象可以为您提供一个框架，用于:

*   正确模拟您的业务问题
*   确定业务验证应该何去何从

# 保持联系

别忘了在 [twitter](https://twitter.com/jamesmh_dev) 或 [LinkedIn](https://www.linkedin.com/in/jamesmhickey/) 上联系我！

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！