# Sitecore Commerce 中一个微妙的名称空间冲突错误

> 原文：<https://dev.to/jermdavis/a-subtle-clash-of-namespace-bug-in-commerce-36la>

最近，我有机会参加了 Sitecore 的“[实施体验商务](https://www.sitecore.com/services-and-support/training/classroom-training/back-end-developers/sitecore-commerce-for-developers)”培训课程，并获得了商务运作细节的认证。当我做这门课的实验练习时，我碰到了一个有趣的 bug，它看起来就像是其他人可能会遇到的那种事情。

因此，如果您正在扩展商业 OData APIs，请小心:

这些练习的实验笔记通常建议您将代码示例复制并粘贴到 Visual Studio 中。但是为了帮助我理解，我输入了大部分代码，以便更加注意它在做什么。在其中一个练习中，您必须创建一个 API 控制器，它可以回答 OData 查询，以获取关于您的目录中的产品的信息。

商业 API 是现代的。Net 核心代码，所以一般来说他们的控制器返回`Task<IActionResult>`。培训中的一个例子有一个控制器，看起来有点像这样:

```
[EnableQuery]
[Route("api/Example")]
public class ExampleController : CommerceController
{
    [EnableQuery]
    [HttpGet]
    [Route("(Id={id})")]
    public async Task<IActionResult> Get(string id)
    {
        ProductExample yourData = await yourCode.ProcessThisOperation(id);
        return new ObjectResult(yourData);
    }
} 
```

输入代码后，我遵循了实验室笔记平淡的断言，即我应该“用`using`语句解决任何命名空间问题”。我接受了 Visual Studio 的智能感知所提出的建议。完成后，我编译并运行了解决方案。并按照指示与[邮递员](https://www.getpostman.com/)一起测试。

这就是令人困惑的地方。我的实例返回了一个最初看起来有效的响应:

```
{  "@odata.context":  "https://localhost:5000/Api/$metadata#Example/$entity",  "CompositeKey":  null,  "DateCreated":  "2019-03-13T16:05:36.7849024Z",  "DateUpdated":  "2019-03-13T16:05:36.7849024Z",  "DisplayName":  "",  "FriendlyId":  null,  "Id":  "fdb359edeb5442839b41ee938fe7ac6b",  "Version":  0,  "IsPersisted":  false,  "Name":  "Entity-ProductExample-Cart01",  "Policies":  []  } 
```

虽然这是一个成功的回应，但却不是正确的。控制器代码正在返回一个自定义类型，该类型至少应该向 json 对象添加一个属性:

```
public class ProductExample : CommerceEntity
{
    [Contained]
    public IEnumerable Products { get; set; }
} 
```

但是“Products”属性并没有出现在我的 json 数据中，尽管当我通过调试器检查时,`Products`属性包含数据。当我和这门课的其他学生比较结果时，他们确实有这些数据。

发生了什么事？

在花了太多时间使用 diff 工具和其他人的代码工作副本后，我发现了一个微妙的错误:当我解析名称空间时，我选错了一个名称空间…

在解析`ObjectResult`类的位置时，我选择了名称空间`System.Web.Http.OData`。这看起来可行，因为那里有一个同名的类。但事实证明，它的行为与生活在`Microsoft.AspNetCore.OData`下的那只有一点点不同。当我在我的代码中改变它时，我开始看到正确的数据:

```
{  "@odata.context":  "https://localhost:5000/Api/$metadata#Example/$entity",  "CompositeKey":  null,  "DateCreated":  "2019-03-13T16:05:36.7849024Z",  "DateUpdated":  "2019-03-13T16:05:36.7849024Z",  "DisplayName":  "",  "FriendlyId":  null,  "Id":  "fdb359edeb5442839b41ee938fe7ac6b",  "Version":  0,  "IsPersisted":  false,  "Name":  "Entity-ProductExample-Cart01",  "Policies":  [],  "Products@odata.context":  "https://localhost:5000/Api/$metadata#Example('fdb359edeb5442839b41ee938fe7ac6b')/Example",  "Products":  [  {  "CompositeKey":  null,  "DateCreated":  "2018-01-18T13:53:30.2133302Z",  "DateUpdated":  "2018-05-27T18:10:16.6754176Z",  "DisplayName":  "Habitat Centerpiece 1.5 Cu. Ft. Countertop Convection Microwave",  "FriendlyId":  "6042749",  "Id":  "Entity-SellableItem-6042749",  "Version":  1,  "ListPrice":  null  }  ]  } 
```

因此，如果您发现自己编写的代码在其返回类型中使用了`ObjectResult`,那么在接受 Visual Studio 关于添加什么名称空间的建议时要非常小心——如果您不使用`Microsoft.AspNetCore.OData`,那么您的代码可能无法正常工作。

不要像我一样 [![😉](img/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png)