# 为测试驱动开发辩护

> 原文：<https://dev.to/cocoroutine/in-defense-of-test-driven-development-4h0e>

## 为测试驱动开发辩护

我仍在像其他人一样学习写伟大的代码。最近有一些抨击 TDD 的文章。我想分享一下 TDD 对我的帮助。

### OOP 和 TDD 很好的一对

好的 OOP 应该遵循可靠和干净的代码原则。类应该更小，方法更小。这使得编写单元测试更加容易，因为每个测试用例要测试的内容要少得多。

Bob 叔叔的 Clean code 和 Sandi Metz 的 POODR 都用了大量的章节来讨论测试。这不是偶然的。测试驱动开发是编写可维护代码的重要部分。如果你在测试驱动开发方面有问题，也许你的类和方法太长了。考虑你的代码是否遵循可靠和干净的代码原则。

### 先测试用例

在 TDD 之前，我所做的是创建一个类，并定义我认为我会需要的属性和方法。我发现这个习惯很难打破，直到我强迫自己在编写一行代码之前创建测试用例。我这样做是为了创建更简单的单元测试。

Python 示例:

```
def test_myobject:

    #check if the attribute exists
    self.assertTrue(hasattr(myobject, 'myattribute'))

    #check the type
    self.assertIsInstance(myobject.mylist, list)

    #check if function
    self.assertTrue(callable(myobject.mymethod)

    #check if attribute defaults to False
    self.assertFalse(myobject.my_false_attribute) 
```

这非常类似于创建一个类的基本结构而不编写实际的类。有些人会认为这些单元测试不测试任何东西，不值得。毕竟，为什么要测试像变量赋值这样的基本语言概念呢？

我认为一个对象的公共 API 是一个契约，应该被测试。这些属性的任何改变都会破坏代码。这是一个简单的解决方法，但是这些单元测试也很容易编写。这使得它几乎是免费。

### 提示

在编写和更新代码时，让您的类和单元测试并排进行。如果更新一个，就更新另一个。

如果您的构造函数包含任何条件赋值逻辑，那么您肯定应该为此编写单元测试。

我只为公共的方法编写单元测试。如果您的私有方法包含一些需要测试的复杂逻辑，也许可以考虑为该方法创建一个 mixin 类，并将其公开。

如果你的测试用例导入了一堆其他的类，这可能表明你测试了太多。在测试代码时也要练习(不要重复自己)。使用 mocks 和 stubs 来代替单元测试所依赖的其他对象，而不是导入其他对象。

### 好处

好的测试用例让我的流程变得更好。现在，我可以快速设计和添加新功能，它们大多在第一次运行时工作。如果我确实有错误，问题是集成而不是单个对象本身。重构也更容易，因为我知道当我破坏东西的时候。

这是对我有用的。祝你一路顺风，一路上帮助他人！