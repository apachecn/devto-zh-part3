# 可调试性被认为是有用的

> 原文：<https://dev.to/janvanryswyck/debugability-considered-useful-176l>

开发者很懒。这没什么新鲜的。我们甚至以此为荣。有时候懒惰是一件好事，但大多数时候这不是我们应该过分吹嘘的事情。让我们来看几个开发者懒惰的例子。

```
public SomeViewModel Map(Something something)
{
    return new SomeViewModel
    {
        Name = something.Name,
        ListA = something.itemsA.Select(itemA => MapItemA(itemA, additionalParameter1, additionalParameter2)).ToList(),
        ListB = something.itemsB.Select(MapItemB).ToList(),
        ListC = something.itemsC.Select(itemC => MapItemC(itemC, additionalParameter3)).ToList(),
        ListD = something.itemsD.Select(itemD => MapItemD(itemD, additionalParameter4)).ToList()
    };
} 
```

这是我们以前都见过的一些代码的例子。见鬼，我经常遇到这种情况。你知道，尽可能内联代码，以避免声明变量。有问题的开发人员可能很难想出变量名。完全可以理解。想出表达意图的名字是我们在编写代码时面临的最困难的事情之一。但这也是懒惰变得相当有害的地方。让我们看看另一个例子。

```
foo.SomeMethod(new Bar(parameter1, parameter2), items.Where(item => item.Property1 == someFilterValue).Select(item => new Buzz { PropertyX = item.Property2, PropertyY = x.Property3 }).ToList()); 
```

这是一个令人讨厌的，不是吗？虽然上一个例子的格式还不错，但这个例子非常糟糕。我见过这样的方法调用，我必须水平滚动几页才能到达结束分号。我有时怀疑这是否仍然是懒惰，或者仅仅是对下一个阅读这段代码的人的憎恨。每次我在代码编辑器中水平滚动时，我都在想我到底做错了什么，应该受到如此冷漠的对待。这些例子传达了对于可读代码的草率和漠视。在这种情况下，我主张提高代码的可调试性。

"调试这段代码会有多难？"这是我们应该更经常问自己的问题。调试这两个例子中的代码片段有多困难？毫无疑问，这是可以做到的。但是这容易吗？还是会比我们想象的要困难一些？

对于第一个例子，假设我们想要检查返回的视图模型对象的 *ListC* 属性的项目。应该在哪里添加断点？对于第二个例子，假设我们想要检查作为第二个参数传递给 SomeMethod 的项目。我们如何做到这一点，而不需要任何额外的努力，如在调试会话期间单步执行方法来检查参数或评估 LINQ 表达式本身？

我绝对不是提倡我们应该花更多的时间使用调试器。事实上，调试代码是一种浪费，我们应该尽可能避免。但是我提倡代码的可调试性。

> “调试毫无价值，但可调试性就是一切。

*—某个聪明人*

我这么说是什么意思？让我们看看前面显示的两个代码示例的改进版本。

```
public SomeViewModel Map(Something something)
{
    var listA = something.itemsA
        .Select(itemA => MapItemA(itemA, additionalParameter1, additionalParameter2))
        .ToList();

    var listB = something.itemsB
        .Select(MapItemB)
        .ToList();

    var listC = something.itemsC
        .Select(itemC => MapItemC(itemC, additionalParameter3))
        .ToList();

    var listD = something.itemsD
        .Select(itemD => MapItemD(itemD, additionalParameter4))
        .ToList();

    var result = new SomeViewModel
    {
        Name = something.Name,
        ListA = listA,
        ListB = listB,
        ListC = listC,
        ListD = listD
    };

    return result;
}

var bar = new Bar(parameter1, parameter2);
var filteredItems = items
    .Where(item => item.Property1 == someFilterValue)
    .Select(item => new Buzz { PropertyX = item.Property2, PropertyY = item.Property3 })
    .ToList()

foo.SomeMethod(bar, filteredItems); 
```

与最初的版本相比，这看起来像是更多的代码。那可能是真的。但是我们也极大地提高了代码的可调试性。对于第一个例子，假设我们想要检查返回的视图模型的 *ListC* 属性的项目。此时，我们甚至有两个选择。我们可以在返回*结果*变量的那一行设置一个断点。但是我们也可以在更早的时候设置一个断点，以便检查 *listC* 变量。第二个例子也是如此，我们有更好的选项来调试代码。

但最重要的是，我们还提高了代码的可读性。我们也不再需要水平滚动来掌握正在发生的事情。如今，像 [Rider](https://www.jetbrains.com/rider/) 这样体面的代码编辑器会在 120 个字符处显示一条垂直线。每当在打字时越过这一行，精神上的警钟应该开始响起，我们应该考虑代码的可读性。

代码易于调试，可读性强，易于推理。因此，我建议我们应该更多地考虑代码质量的这个属性。