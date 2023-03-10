# 你不应该在你的。网络控制器

> 原文：<https://dev.to/makingloops/6-types-of-code-you-shouldnt-have-inside-your-net-controllers-3gml>

“你的。网络控制器应该是瘦的”

不断重复的老生常谈，有 3 公吨的行李要打开。

他们为什么要瘦？这对你有什么好处？如果他们还没有变瘦，我可以采取什么措施让他们变瘦？我如何让他们保持这种状态？

所有有效(和常见)的跟进问题。我在之前的一些文章中已经讨论过*为什么*的*部分*，所以在这篇文章中我们将换一个角度。

为了开始确定瘦控制器所需的步骤，我们需要了解一些他们变胖的常见方式。

根据我的经验，我已经发现了 **6 种潜入我们控制器**的常见代码类型，它们最终会更适合其他地方的*。这个列表并不全面，因为我相信还有更多。*

 *## 1。映射数据传输对象(dto)

因为我们的控制器处于请求管道的第一线，如果请求的要求比简单地使用 URL 参数和 HTTP 状态代码响应更复杂，通常需要创建请求和响应对象。

任何时候你有了 d to，你也需要将它们所包含的值映射到一些更具功能性的东西，比如域实体。

你知道，这种东西:

```
public IActionResult CheckOutBook([FromBody]BookRequest bookRequest)
{
    var book = new Book();

    book.Title = bookRequest.Title;
    book.Rating = bookRequest.Rating.ToString();
    book.AuthorID = bookRequest.AuthorID;

    //...
} 
```

这种*映射*逻辑是足够无害的，但是它迅速膨胀了控制器并增加了额外的责任。理想情况下，我们的控制器的唯一职责就是在 HTTP 请求层委托下一个动作。

## 2。确认

显然，我们不能让不良信息进入我们领域的内部城堡。验证保护我们免受这种影响，通常首先在客户端，然后在服务器端。

我喜欢像对待首席厨师一样对待控制员。他们有助手为他们准备所有的配料，所以他们可以在最后的电镀上施展他们的魔法。在 ASP.NET MVC 中，有很多方法可以沿着请求管道设置验证器，这样**控制器就可以假设请求是有效的，并委托下一个动作**。

这种类型的代码是不可原谅的！

```
public IActionResult Register([FromBody]AutomobileRegistrationRequest request)
{
    // //validating that a VIN number was provided...
    if (string.IsNullOrEmpty(request.VIN))
    {
        return BadRequest();
    }

    //...
} 
```

垃圾！*(用戈登·拉姆齐人的声音说话)*

## 3。业务逻辑

如果控制器中有任何与业务相关的内容，您可能需要在其他地方重新编写。

有时，验证也有重叠。如果您的验证逻辑有业务人员要决定的规则(而不是像数字范围或字符串可以*为*这样的简单事情)，您就要冒必须重复代码的风险。

## 4。批准

授权类似于验证，因为它是一道保护屏障。授权防止不良用户进入他们不被允许的地方，而不是不良请求进入域。

和验证一样，ASP.NET 提供了许多划分授权的方法(例如中间件和过滤器)。)

如果您在控制器动作中检查`User`的属性来授权/阻止某件事，您可能需要做一些重构。

## 5。错误处理

烧起来了，烧起来了！

```
public IActionResult GetBookById(int id)
{
    try
    {
      //the important stuff that a head chef would do...
    }
    catch (DoesNotExistException)
    {
      //stuff an assistant should be doing...
    }
    catch (Exception e)
    {
      //please, no...
    }
} 
```

这一点非常宽泛，有时必须在控制器中处理异常，但我发现几乎总是有更好、更本地化的地方。如果没有，您可以利用**全局异常处理中间件**来捕捉最常见的错误，并向您的客户端返回一致的信息。

回到厨师长的比喻，我喜欢我的厨师长不用担心那种事情。让他们履行自己的职责，并假设其他人会处理任何意想不到的事情。

## 6。数据存储/检索

像使用`Repository`获取或保存实体这样的工作通常会在控制器中结束，以节省时间。如果控制器只是 CRUD 端点，那么嘿，为什么不呢。

我甚至有一篇旧文章展示了具有这种特性的控制器。

我不认为这是一个不好的行为，我认为指出一个替代的方法将会强调为什么这会不必要的膨胀你的控制器。

首先，从设计的角度来看(重点是单一责任原则)，如果您的控制器使用了为持久性设计的对象，那么您的控制器就有不止一个理由进行更改。

```
public IActionResult CheckOutBook(BookRequest request)
{
    var book = _bookRepository.GetBookByTitleAndAuthor(request.Title, request.Author);

    // if you've got the above statement already, you'll be
    // tempted to add the book checkout biz logic here...
    //...

    return Ok(book);
} 
```

除了基本的 CRUD 之外，控制器中的持久性逻辑是一种代码气味，适合更深层次的逻辑。

这就是我喜欢使用某种应用服务(在接口后面)来处理工作，或者委托给某种 CQRS 命令/查询对象的地方。

### 就是这样！

你能想出更多的类型吗？

如果你想更深入，我已经整理了一套带有代码样本的*食谱*和一个专门针对**让你的控制器变瘦的练习**。它不仅仅是*如何*，而是提供了一个*为什么*的深度探索。

可以在原文底部获得[的**一个**。](https://makingloops.com/fat-controller-causes/)*