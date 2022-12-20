# Python 中的不可变对象

> 原文：<https://dev.to/hugh_jeremy/immutable-objects-in-python-4oh>

Python 是一种非常灵活的动物。类属性是可变的，这意味着我们可以在任何时候把它们从任何东西变成任何东西。

```
class Flexible:
    piece = "hello world"

instance = Flexible()
print(instance.piece)  # prints “hello world” instance.piece = 42
print(instance.piece)  # prints “42” 
```

Enter fullscreen mode Exit fullscreen mode

有时，我们可能想用一些灵活性来换取安全性。人类是易犯错误、健忘和善变的野兽。程序员也是人。我们犯的错误比我们愿意承认的要多。

幸运的是，Python 给了我们保护自己免受攻击的工具。只要我们愿意，我们可以用灵活性换取安全性。您可能希望通过创建*不可变对象*来保护自己:一个类的实例一旦被创建就不能被修改。

在本文中，我们将寻求属性的不变性。也就是说，我们将阻止自己改变一个`Flexible`类的`.piece`属性。

通过使我们的类属性不可变，我们消除了推理对象状态的需要。这降低了我们的认知负荷，从而降低了出错的可能性。

注意，这个上下文中的不变性不同于 [himank](https://dev.to/himankbhalla) 在他的[早期开发文章](https://dev.to/himankbhalla/why-should-i-care-about-immutables-in-python-4ofn)中讨论的不变性。在那里，himank 从记忆的角度谈论了不变性，这是一个同样有价值但不同的角度，涉及到不变性的广泛话题。

我们的目标是从程序员的角度实现不变性——明确地捕捉我们无意中试图改变一个不应该改变的属性的情况。从机器的角度来看，该属性仍然是完全可变的。我们并没有试图改变记忆中财产的行为方式，我们只是试图保护自己不被自己的愚蠢所伤害。

为了创建一个不可变的属性，我们将利用内置的 Python [`property`](https://docs.python.org/3/library/functions.html#property) 类。`property`允许我们定义属性的获取和设置行为。

```
class Flexible:
   piece = property(lambda s: "hello world"w)

instance = Flexible()
print(instance.piece)  # prints “hello world” Instance.piece = ‘mutated’  # throws AttributeError 
```

Enter fullscreen mode Exit fullscreen mode

`property`类有四个参数。这里我们将重点关注的两个是`fget`和`fset`。在上面的例子中，`lambda s: “hello world”`是我们的`fget`，允许我们`print(instance.piece)`。当我们试图将`instance.piece`的值设置为`’mutated’`时，`fset`的缺失导致了 AttributeError。

一个 AttributeError 可能足以提醒你自己，你不小心做了一件蠢事。但是，您可能会与多个程序员一起处理一个项目。也许 AttributeError 对于其他人来说并不是一个足够清晰的警告，即属性不应该改变。

例如，一位同事可能会将 AttributeError 解释为您只是忘记实现`fset`的信号。他们可能愉快地编辑你的类，添加`fset`，不知不觉地打开了与状态相关的错误的潘多拉盒子。

为了给我们的同事尽可能多的信息，让我们把不变性显式化。我们可以通过子类化`property`来实现。

```
class Immutable(property):
   _MESSAGE = "Object state must not be mutated"

   def __init__(self, get) -> None:
      super(
         fget,
         self._set_error
      )

   def self._set_error(self, _1, _2) -> None:
       raise RuntimeError(self._MESSAGE) 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们试图改变一个属性时，我们会得到一个清晰明确的错误。

```
class Flexible:
   piece = Immutable(lambda s: "Can't touch this")

instance = Flexible()
instance.piece = "try me"  # Raises RuntimeError with clear description 
```

Enter fullscreen mode Exit fullscreen mode

当然，一个服务于常量的`lambda`并不能满足很多需求。您可以为`fget`参数提供一些更有用的东西。例如，假设一个类维护一些内部状态，整个程序都可读。对于程序的安全运行至关重要的是，类之外的任何东西都不能修改该状态。

```
class Flexible:
   _internal_state = 42
   some_state = Immutable(lambda s: s._internal_state) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，程序的其余部分可以通过`some_state`属性安全地访问`_internal_state`的值。我们通过使用前导下划线向我们的同事提供了一个强烈的暗示，即`_internal_state`是禁止使用的:这是一个暗示变量被视为“私有”的约定。由`some_state`返回的值可以由类在内部修改，但是程序员很难在外部意外修改状态。

其他语言可能以其他方式实现这种行为，特别是通过使用`private`关键字。比如在 Swift:

```
class Flexible {
   public private(set) var some_state = 42
} 
```

Enter fullscreen mode Exit fullscreen mode

与 Swift 等不同，Python 不会显式阻止某人修改`Flexible`状态。例如，一个同事可以轻松地执行

```
instance._internal_state = "where is your god now?" 
```

Enter fullscreen mode Exit fullscreen mode

这种灵活性是 Python 的一大优势。重点不是阻止任何人做任何事。重点是提供有用的暗示、检查和线索来阻止我们犯愚蠢的错误。

*最初发表于[hughjeremy.com](https://hughjeremy.com/blog/immutable-python-objects/)T3】*