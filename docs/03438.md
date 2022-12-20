# 编写更好的测试

> 原文：<https://dev.to/xoubaman/writing-better-tests-4apd>

# 编写更好的测试

在这个系列的最后一章，我们将讨论一些写更好的测试的技巧。

这三篇文章基于我的一次演讲，在谈到最后一部分时，我谈到了几个反模式和具有不同复杂程度和价值的测试方面。过了一段时间后，我意识到最好只关注其中的几个，这是我所面临的最痛苦的情况的根源。

## 测试实现伤害

[![My brain hurts!](img/8a9a0a819adc5a4dcf44c7d8cfd795db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pgN6YXd_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jbext2n9zx0jhwfm8qhf.jpeg)

标题是:

> 测试行为，而不是实现

这是测试中最广为人知的口头禅之一，但是，由于某种原因，它在实践中经常被遗忘。测试实现，尽管它在短期内提供了完成工作的感觉和信心，但从中长期来看将是痛苦的。

这样做的原因是它使重构变得困难，而重构是开发人员最常见的活动。

为了说明测试实现有多棘手，我们将检查一个例子。我们需要实现一些算法，为结帐过程执行总价计算。我们以这个测试结束:

```
public function testCheckoutTotalCalculation(): void
{
    $itemsInBasket = [
        $this->oneItemWorth500,
        $this->anotherItemWorth500
    ];
    $repository    = $this->createMock(CheckoutItemsRespository::class);
    $repository->expects($this->once())
               ->method('fetch')
               ->with(self::BASKET_ID)
               ->willReturn($itemsInBasket);

    $checkoutTotalCalculator = new Calculator($repository);

    $basket          = new Basket(self::BASKET_ID);
    $calculatedTotal = $checkoutTotalCalculator->calculateTotal($basket);

    $expected = 1000;
    self::assertEquals($expected, $calculatedTotal);
} 
```

Enter fullscreen mode Exit fullscreen mode

它看起来不坏，它通过了，所以我们转移到下一个任务，生活是美丽的。然而，这个测试是脆弱的，因为它是在测试实现，并且在不久的将来会伤害我们。

当实现一些不相关的功能时，比如购物篮中的商品列表，它也使用了`CheckoutItemsRepository`，我们决定将存储库改进成这样:

```
public function fetch(int $basketId): ItemCollection { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我们转向集合，而不是普通数组，以利用它们的映射能力。但是，我们之前的测试现在失败了。

总数的计算有变化吗？一点也不。当给出相同的项目时，我们应该期待相同的总数。

测试应该失败吗？不，因为计算没有改变。

测试失败了吗？绝对的。

为什么？我们测试的是实现，而不是行为。

外推至一个相当大的代码库的常见场景，我们可以在重构后结束大量的 red 测试，根据[定义](https://www.martinfowler.com/bliki/DefinitionOfRefactoring.html)，这些测试应该是透明的。

测试实现是一种耦合的方式——这是我们应该尽可能避免的。我们之前的测试与解决价格计算所涉及的所有类紧密相关。所以对它们的改变会导致不相关的失败测试。

每次变更都必须修复大量不相关的测试，这会导致对测试和测试过程的不信任。更有甚者，如果*修复*包括修改模拟期望以适应新的方法签名或返回类型，那么在下一次修改这段代码时，您将会遇到完全相同的问题。

幸运的是，有一些工具可以防止陷入测试实现的陷阱。

## 先试验，后实施

[![Is this egg tested?](img/6e41bad57d4f091926d0e61c221642d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pPDY6Ll6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8vt8fp0kaag139mq66w.jpg)

标题

> 测试优先有助于产生更好的测试和实现

在实现之后编写测试将会在不经意间促使你将它们与你刚刚编写的代码结合起来。你的大脑知道你刚才输入了什么，它会欺骗你嘲笑这两个依赖项，并返回你在编码时所想的确切结果，快速获得一个绿色测试，并继续进行列表上的下一件事。

我们刚刚看到这种测试是多么不受欢迎。在修复了一堆由于与测试无关的代码变更而导致的错误测试之后，我们开始怀疑整个测试是否有意义。

根据我的经验，在生产代码之前编写测试是避免与实现相关的讨厌的脆弱测试的最佳实践。通过先测试，你被迫在实现之前考虑行为。您还将专注于解决手头的问题，而不是从一个实现细节跳到另一个，迷失方向。

在前面的例子中，实现 first 时的思考过程可能是这样的:*“首先，我需要项目，我可以从给定了购物篮 id 的存储库中获取它们...我们还没有这种查找器，还是实现吧(...);好了，现在我拿起东西...等待，但是如果存储库中没有项目，它应该抛出一个异常....这样做是不允许的(...)好的，存储库抛出异常，遍历条目，调用这个 getter，我可以在这个变量中累加总数...等等，更好的是我可以用一个映射器，是的，更干净。现在，让我们编写测试...在我移除变量后，它不再使用*。

首先测试时，更容易关注行为:*“我有两件物品，每件价值 500，总数应该是 1000”*。

你可能会得到一个非常相似的实现，但是一个更解耦的测试，对重构更有弹性。它看起来会像下面这样:

```
public function testCheckoutTotalCalculation(): void
{
    $this->repository->add($this->oneItemWorth500)
    $this->repository->add($this->otherItemWorth500)

    $checkoutTotalCalculator = new Calculator($this->repository);

    $basket          = new Basket(self::BASKET_ID);
    $calculatedTotal = $checkoutTotalCalculator->calculateTotal($basket);

    $expected = 1000;
    self::assertEquals($expected, $calculatedTotal);
} 
```

Enter fullscreen mode Exit fullscreen mode

当首先谈论测试时，不可避免地会谈到测试驱动开发(TDD)。对此我有两点意见。

首先，记住 TDD 不是唯一的测试优先的方法。您可以先进行测试，而不必遵循红绿重构循环。

第二，最近有越来越多的 TDD 批评者。我认为 TDD 是一个令人惊奇的开发工具，并且强烈认为反对 TDD 的主要论点来源于本文中涉及的反模式，以及将教育例子作为严格规则。

网上有大量的文献用简单的案例来解释 TDD，这些案例与您在编写实际软件时通常要处理的事情没有太多的共同之处。如果您采用这些例子并应用相同的模式，而不分析它们对于您当前的代码是好是坏，您可能很容易发现自己陷入了糟糕的测试的陷阱。

现在让我们转到与测试实现密切相关的两个反模式。

## 避开嘲笑

[![Armageddon!](img/9279f85348d89ee39d1b50a9f78ac0f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wWtcrHs7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sonc5z1s5t1py5fhtrh5.jpeg)

标题是:

> 没有必要模仿所有的依赖关系

互联网上关于测试的内容有一个缺陷，那就是过分热情地建议使用模拟。大量的文章鼓励模仿所有的依赖来隔离你正在测试的单元。我认为这里有两个不便之处。首先，模仿所有事物的政策。第二，被测试的单元被隐含地选择为一个单独的类(这就是过度模仿的原因)。因为后者是下一个要讨论的主题，所以现在让我们开始模拟部分。

测试替身是单元级和集成级测试的关键部分。您应该使用它们来缩小代码执行的范围，只执行被测试单元所要求的范围。让我们用例子来说明这一点:

*   当您对用户注册用例进行单元测试时，您将模拟数据库访问，因为这不是您正在测试的行为。
*   当您对用户注册用例的持久性进行集成测试时，您将模拟对除数据库之外的外部系统的任何访问(例如，获取用户电子邮件的脸书集成)。
*   当您在用户注册用例中集成测试脸书通信时，您可能会模拟数据库访问*。

* *在这种情况下，不模仿持久性访问可能是有意义的。*

然而，大部分文献鼓励嘲笑你测试的类的所有合作者。这是脆弱的测试套件的另一个根源，最终会在重构期间导致挫折。

考虑到下面的类:

```
class InvoiceProcessor
{
    private RateRepository $rateRepository;
    private InvoiceRepository $invoiceRepository;
    private MoneyConversor $conversor;
    private MoneyFactory $moneyFactory;

    public function process(InvoiceCollection $invoices): void {}
} 
```

Enter fullscreen mode Exit fullscreen mode

除了为了编造一个例子而采取的一些可疑的设计决策之外，当单元测试发票的处理时，我们应该在这里嘲笑什么呢？

假设存储库正在访问数据库以获取当前汇率和发票，我们应该模拟它们。应该在其他地方测试预期的交互行为。这里我们关注的是发票处理的行为，我们不关心任何持久化机制如何检索数据。通过这种方式，我们保持了单元测试的隔离性、快速性和低成本性。

现在，假设`MoneyConversor`只是采用一些数字汇率，并将其应用于货币数量进行转换，我们应该嘲笑它吗？

那么`MoneyFactory`呢，它只需要数量和货币的两个标量值来构建一个`Money`对象。那么作为参数传递的`InvoiceCollection`呢？

如果他们没有超出我们测试的范围，显然他们没有，我们应该避免嘲笑他们。

为什么？如果我们嘲笑所有这些，我们将测试实现，并且不必要地将测试中的单元与用于完成其预期行为的具体类耦合起来。所有模拟场景中的任何重构都会导致测试失败，因为实现细节发生了变化。这是不可取的。

当我们编写测试代码时，我们必须把我们的测试单元看作一个黑盒。我们知道某个输入的预期结果。从一个到另一个的转换是我们预期的行为。在编写测试时，我们不应该关心代码是如何实现这种行为的。它在黑匣子里面，看不见。当我们被迫了解黑匣子的内部情况时，就是黑匣子的某个部分与某个东西——数据库、消息代理、时间等——进行通信的时候。-我们无法控制或无力控制的。这些部分是我们需要嘲笑的，仅此而已。

这就引出了一个问题，如何定义黑匣子的极限？什么是合适的测试单位？

## 明智地选择测试单位

[![Eggscellent](img/cb031ca8f03b4fb2014c588d0f2102e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jpz-PhSJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h0xvqvdnlym2nvbn42zd.jpeg)

标题是:

> 不要为所有的类创建测试，而是为所有的单元创建测试

但是什么是单位呢？答案是...(要说出令人吃惊或高兴的事情)听着...*看情况*。

然而，对于什么是*而不是*合适的测试单元，至少有一个具体的答案:每一个类。总是遵循类之间 1:1 的关系是将测试耦合到实现的另一种方式。有时，我们会有匹配一个确切类的测试，但这并不是因为一些刻在石头上的规则，而是因为它有意义。

这种 1:1 的规则在某种程度上受到网络文献的鼓励，因为在一个简单的例子中，没有上下文，这是一种有效的方法。在一个更复杂的应用程序中，我们将会遇到几种情况。

考虑这样一个场景，一个类有一个大的公共方法，经过了充分的测试:测试覆盖了行为，而不是实现。当使用测试优先的方法时，这并不罕见。

这个方法太大了，我们将它的一部分重构到其他类中。很好的练习。现在，我们应该对新的、更小的类进行单元测试吗？他们不是已经在我们最初的大班测试中测试过了吗？

我们有两个选择:重复我们已经缩小到较小类的测试，或者在初始类测试中模拟较小的类。这两种选择都会使测试变得脆弱，将它们耦合到实现中，并使它们难以进一步重构。

另一个可能的场景是我们的`InvoiceProcessor`类，它使用了`Amount`值对象。这个`Amount`类用于我们应用程序的其他部分，可能包含一些在`InvoiceProcessor`中不需要的逻辑(例如，在处理发票时，我们需要金额的加法而不是减法)。在这种情况下，为这个类提供测试是有意义的。

在每种情况下决定什么是一个单元可能是棘手的。一个干净的架构很有帮助。

最近，我意识到测试领域逻辑是多么方便，它建立了在用例级别测试的单元。这个具体的应用程序遵循分层架构，其中每个用例在应用层都有一个*处理程序*。处理程序是域的客户端，调用适当的域方法来完成用例。我已经在这些处理程序中设置了大多数要测试的单元，所以域逻辑是通过它们来测试的，没有针对具体域实体的测试。这样，我可以在安全的情况下完成领域中的大规模重构，只要处理程序测试是绿色的，应用程序就会按照预期的方式运行。

但是，前面的例子可能不适用于其他场景。根据每种情况的需要进行分析和选择。

# 总结

最好的总结是标题:

*   测试行为，而不是实现。
*   测试优先有助于产生更好的测试和实现。
*   没有必要模仿所有的依赖关系。
*   不要为你所有的类创建测试，为你所有的单元创建测试。

写这个帖子系列真的很有趣。我希望你和我一样喜欢它。不要犹豫，请在评论中留下您的任何反馈！

# 进一步阅读

*   同样，[由 Steve Freeman 和 Nat Pryce 的测试](http://www.growing-object-oriented-software.com)指导的面向对象软件开发是开始和改进测试的极好资源。
*   如果你不相信 TDD，Fran Iglesias 有一个很好的解释，它甚至对你的精神健康也有好处。