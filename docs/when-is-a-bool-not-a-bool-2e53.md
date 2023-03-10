# 什么时候布尔人不是布尔人？

> 原文：<https://dev.to/evandeaubl/when-is-a-bool-not-a-bool-2e53>

这是一个愚蠢的问题——或者更确切地说，它**应该**是一个愚蠢的问题——但是当你将 Objective-C 和 Swift 代码集成在一起时，它有时会咬你一口( ***咳*** UIKit)。

您将尝试使用您认为是来自 Objective-C API 的`Bool`,突然您得到这个编译错误:

```
Cannot convert value of type ‘ObjCBool’ to expected argument type ‘Bool’ 
```

这个**是**的一个布尔值；为什么我不能用这个@($^的东西作为`Bool`？

通常标准类型在 Objective-C 和 Swift 之间表现良好。但是`Bool` s 是一个不寻常的例子，需要一个不明显的解决方案。

Objective-C 使用`BOOL`作为布尔值的类型。在 Swift 的早期版本中，这被恰当地桥接到了一个`Bool`。在 Swift 3 中，这变成了一个`ObjCBool`类型的值。当你习惯于标准类型被很好地自动桥接时，当你突然拥有你认为是`Bool`的东西，但它不能像`Bool`一样在任何地方使用时，这是不和谐的。您会得到前面提到的错误。它也不能被投射到`Bool`；这也会导致编译时错误。

原来`ObjCBool`实例上有一个属性叫做`boolValue`，负责从`ObjCBool`产生一个 Swift `Bool`。因此，在我遇到问题的例子中，访问 Objective-C 对象的 BOOL 属性:

```
let val = objcObject.aBool // This is of type ObjCBool
let boolVal = objcObject.aBool.boolValue // This is actually a Bool 
```

当 Swift 本身不太像 Swift 时，这就令人恼火了，不是吗？

如果你需要去另一个方向，那就谢天谢地更明显了:

```
let objectiveCBool = ObjCBool(swiftBoolVal) 
```

也许有一天我们能够一起把目标 C 抛在脑后。在那之前，像这样的小变通办法将成为游戏的一部分。

* * *

你喜欢这个建议吗？下一篇关于[通过包含截图或视频差异](https://www.appsdissected.com/faster-ui-code-reviews-screenshot-video-diffs/)来加速 iOS UI 代码审查的技巧已经在等着你了。或者[注册，让每一条建议都直接进入你的收件箱。](https://www.appsdissected.com/newsletter/)

这篇文章最初发表在 [Apps 剖析。](https://www.appsdissected.com/bool-objcbool-swift/)