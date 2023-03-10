# 洋葱编码:分层编程

> 原文：<https://dev.to/turnerj/onion-coding-programming-in-layers-4185>

如果你想知道关于众所周知的[“洋葱计划”](https://www.torproject.org/)的有趣细节，很抱歉让你失望了。取而代之的是，这篇文章将谈论我将代码组织成[层](https://youtu.be/-FtCTW2rVFM?t=44)的经历，以及一些众所周知的模式。

这篇文章不是关于什么是最好的方法，因为我不认为有一个“最好的”方法，即使有，我也不够聪明，不知道那是什么。然而，我希望你从这篇文章中得到的是如何构建当前或未来的代码库的想法。

我将把重点放在 C#上，但是分离和抽象代码的核心思想可以推广到任何其他编程语言。

## [分离顾虑](https://en.wikipedia.org/wiki/Separation_of_concerns)

这是描述代码分层的更“官方”的术语。目标是将特定的任务(“关注点”)相互分离，就像业务逻辑和表示或数据访问一样。

一个简单的看待方式是:X 关心 Y 的实现吗？

*   业务逻辑关心数据持久化的实现吗(比如 MySQL vs MS SQL vs 平面文件)？
*   “表示”关心业务逻辑的实现吗？
*   “表示”关心数据持久化的实现吗？

如果你对以上任何一个问题的回答是肯定的，那么你的代码可能会有一些问题...

### 接口，不实现

有了上面的例子，最好记住问题指的是“实现”，而不是“接口”。如果对你的代码来说，MySQL 和 MSSQL 有完全相同的接口(比如 C#中的 LINQ ),为什么你代码的其他部分要依赖于它是 MySQL 还是 MSSQL 呢？

```
 class Person
{
    public string Name { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string FavouriteColour { get; set; }
}

//An example of a query using LINQ
//No worry about MySQL or MSSQL, just my query in C#
var people = myDataContext.People
    .Where(p => p.FavouriteColour == "Green")
    .OrderBy(p => p.DateOfBirth)
    .GroupBy(p => p.Name); 
```

Enter fullscreen mode Exit fullscreen mode

当你开始走这条路的时候，像[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)这样的话题就出现了。依赖注入可以让你更大程度地利用[接口](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)，并且拥有一个公共根(又名。“复合根”)定义了实现是什么以及它们是如何构造的(例如，特定类的特定配置)。

例如，您可能正在使用 [EF Core](https://docs.microsoft.com/en-us/ef/core/) ，它可以有各种各样的数据库提供者。您的业务逻辑可能会引用 EF Core，但不知道它是 MSSQL、MySQL 还是其他提供商。复合根是设置提供者和其他东西，如连接字符串。

> 詹姆斯·希基[在 2007 年发表了一篇关于依赖注入的好文章。网芯](https://dev.to/jamesmh/net-core-dependency-injection-everything-you-ought-to-know-f23)最近哪些值得一读。

您可能会争辩说，您不会做一些极端的事情，比如更换您将用来支持您的应用程序的数据库提供商，您可能是对的。然而，这并不意味着将业务逻辑从数据库实现中分离出来不是一个好主意。例如，能够模拟持久层来测试业务逻辑是非常有用的。

说了这么多，让我们开始看看应用程序中层的一些具体例子，以及我们如何分离各种关注点。

## MVC 简单如 1-2-3:表示层

[模型-视图-控制器(MVC)](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) ，也许你听说过这个，也许你没有。我对它的简短描述是，用户与做一些“工作”的*控制器*交互，*控制器*然后创建一个输出*模型*，而*模型*帮助生成一个特定的*视图*作为回报。

> [![Wikipedia Diagram of interactions in MVC](img/9e14a03686b9e1be8715c7a0687549fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i4N2-Sj3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/1024px-MVC-Process.svg.png)

这种模式在网站中以某种方式相当普遍——他们是否使用该术语是另一回事。它主要是一种服务器端模式，但是随着更高级的客户端应用程序的构建，这种模式也可以在那里看到。不过，从技术上来说，它并不一定是与 web 相关的——它可以是显示程序输出的任何方法。虽然我的经验实际上只在服务器端与 web 相关(例如 ASP.NET 和一些 PHP 框架)。

虽然这三个部分之间存在某种耦合，但通常可以将其视为独立的关注点。在很大程度上，控制器负责指导用户操作，并协调所发生的事情。控制器不太关心您放入视图的额外的`<div>`或者您在 CSS 中定义的更大的字体。然而，它确实关心将传递给视图的模型的形成。

这不是一个完美的解决方案，但总的来说，如果做得好，这是一个有用的模式。

在小型应用程序中，您可以将动作的所有业务逻辑放入控制器中。它*是*一个可测试的解决方案(如果你喜欢在测试中调用控制器的话),但是当有很多共享代码时，它的伸缩性不好。你可以把它放在一个额外的[协调者/处理者/控制者/管理者](https://stackoverflow.com/q/1866794/1676444)类中，但是那是最好的选择吗？

*也许？*

## 处理业务:业务逻辑层

> [![Bachman-Turner Overdrive - Takin' Care of Business](img/b65acf4b2471dfec256cd06fa945237e.png)](https://www.youtube.com/watch?v=NCIUf8eYPqA) 
> 有趣的事实:我和乐队没有关系...

当你开始把 MVC 仅仅看作一个表示接口时，你可以开始构建一个新的层，在那里你可以真正地放置共享的业务逻辑。当您的应用程序变得更大时，这是需要考虑的事情，您可能有多个接口连接到同一个业务逻辑。这也有助于定义更精确的单元测试，而不是通过 MVC 控制器动作来实现。

> 我的方法受到了[领域驱动设计](https://en.wikipedia.org/wiki/Domain-driven_design)和[“工作单元”](https://www.martinfowler.com/eaaCatalog/unitOfWork.html)模式的启发。如果您正处于构建代码的过程中，它们都绝对值得一看。在本文的后面，我将更详细地介绍“工作单元”。

让我们看一个例子，以及如何/为什么将它从典型的 MVC 层转移到一个更具业务逻辑的层。

```
 interface IAccountService
{
    UserAccount Login(string emailAddress, string password);
}

class AccountService : IAccountService
{
    ...

    public UserAccount Login(string emailAddress, string password)
    {
        var account = Context.Accounts.Where(a => a.EmailAddress == emailAddress).FirstOrDefault();
        if (account == null || !account.VerifyPassword(password))
        {
            throw new AccountLoginException("Incorrect username or password");
        }
        if (!account.IsRegistered)
        {
            throw new AccountRegistrationException("Registration has not been completed");
        }
        return account;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一个登录流可能看起来不像是一个典型的业务逻辑功能，如果你只有一个网站，一个登录流只出现一次。把它拿出来的最大好处是，我们看到的是从处理对用户的响应中抽象出来的登录规则。这使得代码更加模块化，更容易测试。

让我们来看看这个例子的几个具体部分:

1.  如果用户或密码错误，登录不会泄露
2.  如果帐户注册没有完成，登录确实会泄露
3.  `AccountService`实现了`IAccountService`接口

**登录时不会泄露用户名或密码**
像你登录网站时通常会发现的那样，他们通常会说一些类似“无效用户名或密码”的话，以增加隐私和安全性。你不能废弃有账号的用户，或者一旦你有了账号，就强行设置密码。

通过使这个规则处于这个级别，我们说没有消费代码可以知道是用户名还是密码错了。我们向消费代码发出信号(在这种情况下，通过一个异常)表明这失败了，但是我们不负责处理如何将它传递给最终用户。

**如果账户未注册，登录确实会泄露**
这可能看起来与第一项的直觉相反，但这里的关键点是，它会在确认电子邮件地址和密码之前进行检查。要了解这两个细节，你基本上有权限知道你是否通过注册。

请注意，代码并没有尝试重定向用户继续注册。这是因为我们不知道是什么在消耗这些代码。如果我们想要执行重定向，我们可能需要访问`HttpContext`，但是如果这是一个本地运行的 CLI 应用程序，那就没有任何意义了。

这个函数甚至不跟踪用户是否登录，因为这不是它所关心的。

像第一项一样，这里的关注点分离使得*检查登录*远离*处理用户响应*。

**`AccountService`实现了`IAccountService`接口**
这可以追溯到之前的依赖注入讨论，我们可能有一个类似上面的网站实现，从 MVC 控制器调用。我们可能还为 CLI 应用程序实现了，它对所述 MVC 控制器进行 API 调用。

这显示了 DI 如何通过让消费代码不知道具体的实现来帮助分离关注点。

至此，我们已经讨论了表示层和业务层，现在来探索数据持久性的世界，以及我们如何拥有更多的代码层。

## 持久化是关键:数据层

当您在研究抽象数据时，您可能会看到的一个常见短语是工作单元和/或存储库模式。

### [储存库模式](https://deviq.com/repository-pattern/)

该模式涉及每个业务对象的“存储库”类，负责与数据存储交互——无论是数据库还是平面文件。这个类通常会有一般的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作，尽管你也可以有特定的函数，比如“ListActive ”,只列出“活动”记录。

### [工作单位](https://www.codeproject.com/Articles/581487/%2fArticles%2f581487%2fUnit-of-Work-Design-Pattern)

这将存储库模式向前推进了一步，将存储库包装成单一类型的事务。比方说，你有一个客户下订单，一个“工作单元”既可以下订单，也可以相应地减少当前的库存。

> 您可能会认为工作单元更多的是业务逻辑，而不是数据，这种想法不会错。在某些方面，我们正在实施业务特定的决策，如上面的股票计数示例。在其他情况下，我们只是试图确保数据的一致性。我个人看更多的是后者。

在 C#中，如果你使用过[实体框架](https://docs.microsoft.com/en-us/ef/)，你可能已经处理过这两种模式了。它使用了一个`DbContext` ( [可以看作是一个工作单元](https://docs.microsoft.com/en-us/dotnet/api/system.data.entity.dbcontext?view=entity-framework-6.2.0))和一系列`DbSet`属性(可以看作是存储库)。考虑到这一点，在它上面实现相同的模式似乎有些多余(就我个人而言，我不会这样做)，但是如果你想从你的业务逻辑中抽象出实体框架，你可以再次包装它。

> [![Opens a box, which inside is the room with a box, opens that box which inside is another room, recursively](img/0d1a4addd13602c55d191fab2029392f.png)](https://giphy.com/gifs/perfect-loops-wt0bJ5t4cgxm3b8AC4) 
> *只需要再多包裹一层就可以了...*

再次包装它对测试确实有好处，因为它可以更容易地允许您模拟工作单元。然而，这就是我们需要开始思考的地方，继续抽象代码库对于维护来说是好是坏。

## 抽象思维:有没有太多这种东西？

### *看情况...*

抽象代码和分离关注点有很多好处，包括增加测试特定关注点的能力和整个系统的灵活性。

虽然每个业务对象的代码库增加了几个额外的类，但是也有不好的一面，您可能会有一些笨拙的组合根(如果您使用 DI ),并且/或者您可能最终会有 100 多个类和函数，其中只有 1 或 2 行代码。

有些人可能认为这是一件好事，但我不是其中之一。我确实喜欢在我的函数中加入一些内容，所以当我将一些东西抽象掉的时候，我确实会考虑一个特定的抽象和分离是使代码更容易管理还是更难管理。

### 您的里程可能会有所不同

归根结底，分离和抽象的水平取决于许多事情。这篇文章并不是要告诉你哪种方式是最好的，只是给你一些思考。