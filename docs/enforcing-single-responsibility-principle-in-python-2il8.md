# 在 Python 中实施单一责任原则

> 原文：<https://dev.to/wemake-services/enforcing-single-responsibility-principle-in-python-2il8>

**最初发表于我的博客**:【https://sobolevn.me/2019/03/enforcing-srp T2】

单一责任原则(SRP)是软件开发中最重要的概念之一。这个概念的主要思想是:所有的软件必须只有一个单一的责任。

为什么 SRP 很重要？这是软件开发背后的主要思想。将复杂的任务分解成一组简单的构建块，然后再将它们组合成复杂的软件。就像我们可以组成乐高或内置功能:

```
print(int(input('Input number: '))) 
```

本文将指导您完成编写简单代码的复杂过程。我个人认为这篇文章相当复杂，如果你没有扎实的`python`背景，很难理解，所以分成几个部分:

1.  简单构造块的定义
2.  `python`中功能组成的问题
3.  介绍解决函数组合问题的可调用对象
4.  依赖注入减少了可调用对象的样板代码

每读完一篇文章后停下来，修改一下，再看下一篇，这是没问题的，因为这将确保你理解重点，或者至少第一次校对是这样的。请不要犹豫提出修改建议或问题。这个话题没有被很好地涵盖，我将很高兴澄清任何事情。

## 定义积木

让我们从定义我所说的这些“软件片段”和“最简单的构建模块”开始。

最简单的构件通常是语言的表达和陈述。我们可以用它来创作任何东西。但是，我们不能完全依赖它们，因为它们太简单了。我们不希望到处都是重复的代码。因此，我们发明了一些函数，将这些最简单的语言结构抽象成更有意义的东西，让我们可以实际使用。

我们期望这些最简单的构件(读“函数”)是可组合的。为了易于组合，它们必须遵守单一责任原则。否则，我们会有麻烦。因为当你只需要其中的一部分时，你不能组合出做几件事的东西。

## 功能也可以很复杂

现在，让我们确保我们真的可以依赖函数作为简单的构建块。

我们可能已经知道函数也会变得复杂，我们都见过像[这个](https://github.com/sobolevn/python-code-disasters/blob/7f8c856073e746cb79193df90ba2eb5f2eb144e7/python/create_objects.py#L1) :
这样完全不可读的函数

```
def create_objects(name, data, send=False, code=None):
    data = [r for r in data if r[0] and r[1]]
    keys = ['{}:{}'.format(*r) for r in data]

    existing_objects = dict(Object.objects.filter(
        name=name, key__in=keys).values_list('key', 'uid'))

    with transaction.commit_on_success():
        for (pid, w, uid), key in izip(data, keys):
            if key not in existing_objects:
                try:
                    if pid.startswith('_'):
                        result = Result.objects.get(pid=pid)
                    else:
                        result = Result.objects.filter(
                            Q(barcode=pid) | Q(oid=pid)).latest('created')
                except Result.DoesNotExist:
                    logger.info("Can't find result [%s] for w [%s]", pid, w)
                    continue

                try:
                    t = Object.objects.get(name=name, w=w, result=result)
                except:
                    if result.container.is_co:
                        code = result.container.co.num
                    else:
                        code = name_code
                    t = Object.objects.create(
                        name=name, w=w, key=key,
                        result=result, uid=uid, name_code=code)

                    reannounce(t)

                    if result.expires_date or (
                          result.registry.is_sending
                          and result.status in [Result.C, Result.W]):
                        Client().Update(result)

                if not result.is_blocked and not result.in_container:
                    if send:
                        if result.status == Result.STATUS1:
                            Result.objects.filter(
                                id=result.id
                            ).update(
                                 status=Result.STATUS2,
                                 on_way_back_date=datetime.now())
                        else:
                            started(result)

            elif uid != existing_objects[key] and uid:
                t = Object.objects.get(name=name, key=key)
                t.uid = uid
                t.name_code = name_code
                t.save()
                reannounce(t) 
```

它真的工作，并为某人的生产系统提供动力。但是，我们仍然可以说，这个功能肯定不止一个责任，应该重构。但是我们如何做出这个决定呢？有不同的正式方法来跟踪这样的功能，包括:

*   [圈复杂度](https://en.wikipedia.org/wiki/Cyclomatic_complexity)
*   [霍尔斯特德复杂度](https://en.wikipedia.org/wiki/Halstead_complexity_measures)
*   参数、语句、返回计数
*   体长限制

在我们应用了这些方法之后，我们就会清楚这个函数太复杂了。我们将无法轻松地创作它。有可能(也建议)进一步自动化这个过程。这就是代码质量工具的工作方式，以 [`wemake-python-styleguide`](https://github.com/wemake-services/wemake-python-styleguide) 为例。

就用吧。它会检测所有隐藏的复杂性，不会让你的代码腐烂。

下面是一个不太明显的例子，一个函数做了几件事，破坏了 SRP(遗憾的是，这样的事情根本不能自动化，代码审查是发现这类问题的唯一方法):

```
def calculate_price(products: List[Product]) -> Decimal:
    """Returns the final price of all selected products (in rubles)."""
    price = 0
    for product in products:
       price += product.price

    logger.log('Final price is: {0}', price)
    return price 
```

看这个`logger`变量。它是如何进入函数体的？这不是争论。这只是一种硬编码的行为。如果出于某种原因，我不想记录这个具体价格，该怎么办？我应该用参数标志禁用它吗？

如果我尝试这样做，我会以这样的方式结束:

```
def calculate_price(products: List[Product], log: bool = True) -> ...
    ... 
```

恭喜，现在我们的代码中有了一个众所周知的反模式。不要使用布尔标志。他们是坏人。

此外，我如何测试这个功能？如果没有这个`logger.log`调用，它将是一个完全可测试的纯函数。有东西进去，我可以预测会有什么东西出来。现在它是不纯洁的。为了测试`logger.log`是否真的可以工作，我必须以某种方式模拟它，并断言日志已被创建。

你可能会争辩说`python`中的`logger`有一个全局配置只是为了这种情况。但是，对于同样的问题，这是一个肮脏的解决方案。

就因为一句台词就这么乱！这个功能的问题是很难注意到这种双重责任。如果我们将这个函数从`calculate_price`重命名为适当的`calculate_and_log_price`，很明显这个函数不考虑 SRP。规则就是这么简单:如果“正确且完整”的函数名包含`and`、`or`或`then`，那么它就是重构的一个很好的候选。

好吧，这一切都很吓人，但是一般来说，这个案子该怎么处理呢？我们如何改变这个函数的行为，使它最终遵守 SRP？

我认为实现 SRP 的唯一方法是组合:将不同的功能组合在一起，这样每个功能只做一件事情，但是它们的组合可以做我们想要的所有事情。

让我们看看在`python`中可以用来组合函数的不同模式。

### 装修工

我们可以使用装饰模式将函数组合在一起。

```
@log('Final price is: {0}')
def calculate_price(...) -> ...:
    ... 
```

这种模式有什么后果？

1.  它不只是组成，而是将功能粘合在一起。这样你就没有能力在没有 T1 的情况下运行 T0
2.  它是静态的。你不能从调用点改变事情。或者您必须在实际的函数参数之前将参数传递给装饰函数
3.  它会产生视觉噪音。当装饰者的数量增加时——大量多余的线条会污染我们的功能

总而言之，装饰者在特定的情况下很有意义，但不适合其他人。好的例子有:[`@login_required`](https://docs.djangoproject.com/en/2.1/topics/auth/default/#the-login-required-decorator)[`@contextmanager`](https://docs.python.org/3/library/contextlib.html#contextlib.contextmanager)，以及好友。

### 功能成分

它非常类似于 decorator 模式，唯一的例外是它是在运行时应用的，而不是在“导入”时。

```
from logger import log

def controller(products: List[Product]):
    final_price = log(calculate_price, message='Price is: {0}')(products)
    ... 
```

1.  使用这种方法，我们可以很容易地按照我们实际想要的方式调用函数:使用我们的 without `log`部分
2.  另一方面，它会产生更多的样板文件和视觉噪音
3.  由于样板文件的数量较多，而且因为您将组合而不是声明委托给了调用者，所以很难重构

但是，它也适用于某些情况。比如我一直用 [`@safe`](https://returns.readthedocs.io/en/latest/pages/functions.html#safe) 函数:

```
from returns.functions import safe

user_input = input('Input number: ')

# The next line won't raise any exceptions: safe_number = safe(int)(user_input) 
```

你可以在另一篇文章中读到更多关于[为什么异常可能对你的业务逻辑有害的信息。我们还在`returns`库中提供了一个实用的类型安全](https://sobolevn.me/2019/02/python-exceptions-considered-an-antipattern) [`compose`](https://returns.readthedocs.io/en/latest/pages/functions.html#compose) 函数，您可以在运行时使用它来编写东西。

### 传递参数

我们总是可以传递论点。就这么简单！

```
def calculate_price(
    products: List[Product],
    callback=Callable[[Decimal], Decimal],
) -> Decimal:
    """Returns the final price of all selected products (in rubles)."""
    price = 0
    for product in products:
       price += product.price

    return callback(price) 
```

然后我们可以调用它:

```
from functools import partial

from logger import log

price_log = partial(log, 'Price is: {0}')
calculate_price(products_list, callback=price_log) 
```

而且效果很好。现在我们的函数对日志一无所知。它只计算价格并返回它的回调。我们现在可以提供任何回调，而不仅仅是`log`。它可能是任何接收一个`Decimal`并返回一个:
的函数

```
def make_discount(price: Decimal) -> Decimal:
    return price * 0.95

calculate_price(products_list, callback=make_discount) 
```

看到了吗？没问题，只要按照你喜欢的方式编写函数。这种方法隐藏的缺点在于函数参数的性质。我们必须显式地传递它们。如果调用栈很大，我们需要传递很多参数给不同的函数。并且可能覆盖不同的情况:我们需要在`a`的情况下回调`A`，在`b`的情况下回调`B`。

当然，我们可以尝试以某种方式修补它们，创建更多的函数来返回更多的函数，或者到处用`@inject`装饰器污染我们的代码，但我认为这是丑陋的。

未解决的问题:

1.  混合逻辑参数和依赖参数，因为我们同时传递它们，很难区分什么是什么
2.  如果调用栈很大，显式参数可能很难或不可能维护

为了解决这些问题，让我向您介绍一下可调用对象的概念。

## 分离逻辑和依赖关系

在我们开始讨论可调用对象之前，我们需要讨论对象和 OOP，记住 SRP。我在 OOP 的主要思想中发现了一个主要问题:“让我们将数据和行为结合在一起”。对我来说，这明显违反了 SRP，因为对象被设计成同时做两件事:它们包含它们的状态**和**已经执行了一些附加的行为。当然，我们会用可调用对象来消除这个缺陷。

可调用对象看起来像具有两个公共方法的常规对象:`__init__`和`__call__`。它们遵循特定的规则，这些规则使它们与众不同:

1.  仅处理构造函数中的依赖关系
2.  仅处理`__call__`方法中的逻辑参数
3.  没有可变状态
4.  没有其他公共方法或任何公共属性
5.  没有父类或子类

实现一个可调用对象的直接方法是这样的:

```
class CalculatePrice(object):
    def __init__(self, callback: Callable[[Decimal], Decimal]) -> None:
        self._callback = callback

    def __call__(self, products: List[Product]) -> Decimal:
        price = 0
        for product in products:
            price += product.price
        return self._callback(price) 
```

可调用对象和函数的主要区别在于，可调用对象有一个传递依赖关系的明确步骤，而函数将常规逻辑参数和依赖关系混合在一起(你已经可以注意到可调用对象只是部分函数应用的一个特例):

```
# Regular functions mix regular arguments with dependencies: calculate_price(products_list, callback=price_log)

# Callable objects first handle dependencies, then regular arguments: CalculatePrice(price_log)(products_list) 
```

但是，给定的例子并不遵循我们强加在可调用对象上的所有规则。特别是，它们是可变的，可以有子类。让我们也解决这个问题:

```
from typing_extensions import final

from attr import dataclass

@final
@dataclass(frozen=True, slots=True)
class CalculatePrice(object):
    _callback: Callable[[Decimal], Decimal]

    def __call__(self, products: List[Product]) -> Decimal:
        ... 
```

现在添加了限制该类成为子类的 [`@final`装饰器](https://sobolevn.me/2018/07/real-python-contants)和具有 [`frozen`](http://www.attrs.org/en/stable/examples.html#immutability) 和 [`slots`](http://www.attrs.org/en/stable/examples.html#slots) 属性的 [`@dataclass`](https://www.attrs.org/en/stable/) 装饰器，我们的类遵守我们在开始时强加的所有规则。

1.  仅处理构造函数中的依赖关系。没错，我们只有声明性依赖，构造函数是由`attrs`为我们创建的
2.  在`__call__`方法中只处理逻辑参数。根据定义，这是真的
3.  没有可变状态。没错，因为我们使用了`frozen`和`slots`
4.  没有其他公共方法或任何公共属性。大多数情况下，我们不能通过声明`slots`属性和声明受保护的实例属性来拥有公共属性，但是我们仍然可以拥有公共方法。考虑为此使用棉绒
5.  没有父类或子类。的确，我们显式地继承了`object`并标记了这个类`final`，所以任何子类都将受到限制

它现在可能看起来像一个物体，但它肯定不是一个真实的物体。它不能有任何状态、公共方法或属性。但是，单一责任原则是伟大的。首先，它没有数据**和**行为。纯粹行为而已。其次，这样很难把事情搞砸。你总是有一个方法来调用你所有的对象。这就是 SRP 的意义所在。只要确保这个方法不太复杂，只做一件事。记住，没有人阻止你创建受保护的方法来分解`__call__`行为。

然而，我们还没有解决将依赖关系作为参数传递给函数(或可调用对象)的第二个问题:嘈杂的显式性。

## 依赖注入

[DI 模式](https://en.wikipedia.org/wiki/Dependency_injection)在`python`世界之外广为人知并被广泛使用。但是，由于某种原因，在里面不是很受欢迎。我认为这是一个应该修复的错误。

让我们看一个新的例子。想象一下，我们有明信片发送应用程序。用户创建明信片，在特定的日期发送给其他用户:节假日、生日等。我们还对其中有多少是出于分析目的而发送的感兴趣。让我们看看这个用例会是什么样子:

```
from project.postcards.repository import PostcardsForToday
from project.postcards.services import (
   SendPostcardsByEmail,
   CountPostcardsInAnalytics,
)

@final
@dataclass(frozen=True, slots=True)
class SendTodaysPostcardsUsecase(object):
    _repository: PostcardsForToday
    _email: SendPostcardsByEmail
    _analytics: CountPostcardInAnalytics

    def __call__(self, today: datetime) -> None:
        postcards = self._repository(today)
        self._email(postcards)
        self._analytics(postcards) 
```

接下来，我们必须调用这个可调用的类:

```
# Injecting dependencies: send_postcards = SendTodaysPostcardsUsecase(
    PostcardsForToday(db=Postgres('postgres://...')),
    SendPostcardsByEmail(email=SendGrid('username', 'pass')),
    CountPostcardInAnalytics(source=GoogleAnalytics('google', 'admin')),
)

# Actually invoking postcards send: send_postcards(datetime.now()) 
```

这个例子清楚地说明了这个问题。我们有很多依赖相关的样板文件。每当我们创建一个`SendTodaysPostcardsUsecase`的实例时，我们必须创建它的所有依赖项。一直深入下去。

所有这些样板文件似乎都是多余的。我们已经在我们的类中指定了所有类型的预期依赖关系。以及我们类的依赖关系中的传递依赖关系等等。为什么我们要再次复制这段代码？

实际上，我们不需要。我们可以使用某种 DI 框架。我个人可以推荐 [`dependencies`](https://github.com/dry-python/dependencies) 或者 [`punq`](https://github.com/bobthemighty/punq) 。它们的主要区别在于它们如何解析依赖关系:`dependencies`使用名称而`punq`使用类型。在这个例子中，我们会选择`punq`。

不要忘记安装它:

```
pip install punq 
```

现在我们的代码可以被简化，这样我们就不必去处理依赖关系了。我们创建了一个注册所有依赖项的地方:

```
# project/implemented.py 
import punq

container = punq.Container()

# Low level dependencies: container.register(Postgres)
container.register(SendGrid)
container.register(GoogleAnalytics)

# Intermediate dependencies: container.register(PostcardsForToday)
container.register(SendPostcardsByEmail)
container.register(CountPostcardInAnalytics)

# End dependencies: container.register(SendTodaysPostcardsUsecase) 
```

然后到处用:

```
from project.implemented import container

send_postcards = container.resolve(SendTodaysPostcardsUsecase)
send_postcards(datetime.now()) 
```

实际上没有重复的样板文件、可读性和开箱即用的类型安全。我们现在不必手动将任何依赖关系连接在一起。它们将由`punq`的注释连接。只需按照您需要的方式在可调用对象中键入您的声明性字段，在容器中注册依赖项，就可以开始了。

当然，有一些高级的输入模式可以更好地反转控制，但是在 [`punq`的文档](https://punq.readthedocs.io/en/latest/)中会有更好的介绍。

## 不使用可调用对象时

很明显，所有的编程概念都有其局限性。可调用对象不应该用在你的应用程序的基础结构层。因为有太多现有的 API 不支持这种类和 API。在您的业务逻辑中使用它，使它更具可读性和可维护性。

考虑将 [`returns`](https://github.com/dry-python/returns) 库添加到组合中，这样[也可以排除异常](https://sobolevn.me/2019/02/python-exceptions-considered-an-antipattern)。

## 结论

我们走了很长的路。从做可怕事情的绝对混乱的函数到遵守单一责任原则的简单的依赖注入的可调用对象。在这个过程中，我们发现了不同的工具、实践和模式。

但是我们的努力有没有带来很大的改变呢？问自己最重要的问题:经过这些重构后，我的代码更好了吗？

我的回答是:是的。这对我来说是一个重大的改变。我可以轻松地将简单的构件组合成复杂的用例。它是类型化的、可测试的和可读的。

你怎么想呢?请在下面的评论中分享你的观点。

关键要点:

1.  使用容易组合的简单构件
2.  要成为可组合的，所有的实体应该只负责一件事
3.  使用代码质量工具来确保这些块是真正“简单”的
4.  为了让高层的东西只负责一件事——使用简单块的组合
5.  使用可调用对象来处理组合依赖关系
6.  使用依赖注入来减少合成的样板文件

就是这样！