# 拉勒韦尔提示:为测试打造你自己的特质“钩子”

> 原文：<https://dev.to/slyfirefox/laravel-tips-making-your-own-trait-hooks-for-tests-49jn>

<figure>[![](img/06798fd4743da1b68a40e56a2088b989.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BeYSCCSx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ApD4NgbTRldsYzxiT) 

<figcaption>照片由[卡梅隆·柯比](https://unsplash.com/@ckirby?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

当编写特性测试时，通常会有许多样板代码需要添加到测试中。Laravel 的默认测试框架是 PHPUnit，这是一个可靠的测试工具，尽管有时有点困难。它在很大程度上基于 JUnit，这是一个针对 Java 库的测试工具。通常，如果我们想在测试设置中添加一些东西，我们必须实现一个设置方法，如果我们在大量测试中使用同一个方法，这可能会有点乏味。当然，我们也可以只在 TestCase 类的 setup 方法中添加一些东西，但这将为每个测试运行，有时我们只需要为特定的测试运行。幸运的是，Laravel 已经有了一个嵌入到它自己的测试用例中的机制，我们可以修改它来根据我们的特性打开和关闭。

#### 一个榜样的特质

所以首先我们需要一个特征。对于这个例子，我将使用一些简单的东西，几乎每个人都必须这样做，如果进行功能测试，生成用户进行身份验证。

通常一段测试代码可能是这样的:

```
$user = factory(User::class)->create();

$this->actingAs($user)
    ->get('/home')
    ->assertOk(); 
```

Enter fullscreen mode Exit fullscreen mode

这可能有点多余，因为我们的测试变得越来越复杂，我们不得不为每个测试创建一个用户进行身份验证。相反，我们将把它放入一个特征中，这个特征将在设置时为每个测试创建用户。