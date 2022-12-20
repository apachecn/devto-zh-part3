# 引入门类(设计模式)

> 原文：<https://dev.to/jamesmh/introducing-gate-classes-4j6a>

*原帖[我的技术博客](https://www.blog.jamesmichaelhickey.com/Introducing-Gate-Classes/)T3】*

你听说过安全条款吗？史蒂夫·史密斯在他的每周开发技巧中讨论了这些问题。

今天，我想向您展示一种我一直在使用的技术，它类似于 guard 子句模式，但用于更高级的场景。这些场景包括当您由于外部依赖性或 guard 子句中需要的复杂逻辑而出现问题时。

对于保护子句，我们将采用这样嵌套条件逻辑的代码:

```
if(order != null)
{
    if(order.Items != null)
    {
        this.PlaceOrder(order);
    }
    else {
        throw new ArgumentNullException("Order is null");
    }
}
else {
    throw new ArgumentNullException("Order is null");
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将反转条件逻辑，并尝试“快速失败”:

```
if(order?.Items == null)
{
    throw new ArgumentNullException("Order is null");
}

this.PlaceOrder(order); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将立即尝试使该方法失败。我说的“衰得快”就是这个意思。

接下来，我们可能会由此创建一个可重用的方法:

```
public static void IsNullGuard(this object me, string message)
{
    if(me == null)
    {
        throw new ArgumentNullException(message);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以在任何需要的地方使用这个保护子句:

```
order?.Items.IsNullGuard("Order is null");
this.PlaceOrder(order); 
```

Enter fullscreen mode Exit fullscreen mode

这将使我们的代码更加整洁，避免任何嵌套条件，并且更容易推理！

# 有依赖包袱怎么办？

我喜欢这个图案。

但是，有时您可能会发现自己试图构建一种具有某些外部依赖性的 guard 子句，比如存储库或`HttpClient`。也许实际守卫的逻辑也相当复杂。

示例可能包括确定是否:

*   用户具有查看系统中特定资源的适当权限
*   潜在订单能够被购买(在简单的零售系统中)
*   保险索赔能够被批准
*   等等。

在这种情况下，我喜欢使用我所谓的“门类”它们就像保护条款，但它们是类...去想想。

把这些想象成系统中每个请求都必须通过的一系列门(就像中间件一样)。如果其中任何一个失败了，门就关闭了，请求就不能再继续了。

让我告诉你我的意思。

# 检查我们是否可以批准保险索赔

假设我们正在构建保险处理系统的一部分。

接下来，我们必须检查索赔是否能够被批准，如果是，就批准它。

这是我们的用例(干净的架构)，或者，正如一些人可能知道的，我们的命令(CQRS):

```
public class ApproveInsuranceClaimCommand : IUseCase
{
    private readonly IInsuranceClaimRepository _claimRepo;
    private readonly IUserRepository _userRepo;

    public ApproveInsuranceClaimCommand(
        IInsuranceClaimRepository claimRepo, 
        IUserRepository userRepo
    )
    {
        this._claimRepo = claimRepo;
        this._userRepo = userRepo;
    }

    public async Task Handle(Guid claimId, int approvingUserId)
    {
        User user = await this._userRepo.Find(approvingUserId);

        // 1\. Figure out if the user has permission to approve this...

        InsuranceClaim claim = await this._claimRepo.Find(claimId);

        // 2\. Figure out if the claim is approvable...

        claim.Approve();
        await this._claimRepo.Update(claim);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于我所做的评论中的逻辑，如果我们需要更多的库来做这些检查呢？

此外，如果我们系统中的其他用例需要进行同样的检查呢？

也许我们有另一个用例叫做`ApproveOnHoldInsuranceClaimCommand`，它将批准一个由于某种原因而被搁置的保险索赔，直到客户提供进一步的文档？

或者，也许在其他用例中，我们需要检查用户是否能够拥有更改声明的权限？

这将导致混乱的代码和大量的复制和粘贴！

# 来自外界的解决办法

就像 guard 子句重构模式一样，我们为什么不做同样的事情，但是将每个 guard 子句转换成一个全新的类呢？

好处是我们可以使用依赖注入来注入任何依赖，比如存储库、`HttpClient`等等。那个**只有**每个门等级才会要求。

现在，我们的用例可能看起来像这样(记住每个门类可能在里面做一些复杂的逻辑):

```
public class ApproveInsuranceClaimCommand : IUseCase
{
    private readonly IInsuranceClaimRepository _claimRepo;
    private readonly CanUserApproveInsuranceClaimGate _canUserApprove;
    private readonly CanInsuranceClaimBeApprovedGate _claimCanBeApproved;

    public ApproveInsuranceClaimCommand(
        IInsuranceClaimRepository claimRepo
        CanUserApproveInsuranceClaimGate canUserApprove, 
        CanInsuranceClaimBeApprovedGate claimCanBeApproved
    )
    {
        this._claimRepo = claimRepo;
        this._canUserApprove = canUserApprove;
        this._claimCanBeApproved = claimCanBeApproved;
    }

    public async Task Handle(Guid claimId, int approvingUserId)
    {
        await this._canUserApprove.Invoke(approvingUserId);
        InsuranceClaim claim = await this._claimRepo.Find(claimId);
        await this._claimCanBeApproved.Invoke(claim);
        claim.Approve();
        await this._claimRepo.Update(claim);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，不再需要`IUserRepository`了，因为它将由`CanUserApproveInsuranceClaimGate` gate 类(和 DI)处理。

*注:为什么我没有给每个门类做一个接口？为了简单起见。但是，是的，通过使用接口而不是具体的类，你可以更容易地模拟它们进行测试。*

# 创建门类

让我们看看如何构建`CanInsuranceClaimBeApprovedGate`门类:

```
public class CanInsuranceClaimBeApprovedGate
{
    private readonly IInsuranceClaimAdjusterRepository _adjusterRepo;
    private readonly IInsuranceClaimLegalOfficeRepository _legalRepo;

    public CanInsuranceClaimBeApprovedGate(
        IInsuranceClaimAdjusterRepository adjusterRepo,
        IInsuranceClaimLegalOfficeRepository legalRepo
    )
    {
        this._adjusterRepo = adjusterRepo;
        this._legalRepo = legalRepo;
    }

    public async Task Invoke(InsuranceClaim claim)
    {
        // Do some crazy logic with the data returned from each repository!

        // On failure, throw a general gate type exception that can be handled 
        // by middleware or a global error handler somewhere at the top of your stack.
        throw new GateFailureException("Insurance claim cannot be approved.")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个门类要么成功，要么失败。

失败时，它将抛出一个异常，该异常将被堆栈捕获。在 web 应用程序中，通常有一些全局异常处理程序或中间件可以将它们转换成特定的 HTTP 错误响应等。

如果我们确实需要在其他地方使用这个逻辑，如上所述，那么我们不需要重新导入这个逻辑所需的所有依赖项。我们可以简单地按原样使用 gate 类，并允许 DI 机制为我们插入所有的依赖项。

# 一些注意事项

值得一提的是，在某些情况下，您的用例以及您的 gate 类*可能需要调用相同的存储库方法*。您不希望两次获取数据(一次在您的 gate 类中，一次在您的用例中)。

在这种情况下，有办法解决。

一种是使用装饰模式构建一个**缓存存储库。**

您可以将它作为一个限定范围的依赖项(在。所以缓存的数据将只在 HTTP 请求的生命周期内被缓存。或者您可以在缓存上设置一个超时。

另一种方法是**允许用例将原始数据作为依赖项**注入 gate 类。

无论如何，这种模式非常有助于使你的代码更容易测试、使用和维护！

# 你有什么想法？

你以前见过这种技术吗？有什么想法吗？我很想听听！

# 保持联系

不要忘记通过以下方式与我联系:

*   [推特](https://twitter.com/jamesmh_dev)
*   [LinkedIn](https://www.linkedin.com/in/jamesmhickey/)

你也可以在我的网站[www.jamesmichaelhickey.com](https://www.jamesmichaelhickey.com)找到我。

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！