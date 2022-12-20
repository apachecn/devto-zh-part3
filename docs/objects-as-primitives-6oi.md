# 作为原语的对象？

> 原文：<https://dev.to/isaacdlyman/objects-as-primitives-6oi>

我琢磨了一个奇怪的想法有一段时间了，我想把它拿出来讨论。我称之为“作为原语的对象”，尽管如果你喜欢这种矛盾修饰法，你也可以称之为“严格类型的松散类型”。

想法是这样的:严格类型语言(比如说 C#)有几个内置的接口，允许一个对象在特定的上下文中作为原语进行计算。演示这一点的最佳方式是一个代码示例:

```
class Animal : IAsString, IAsBoolean {
  private string name;

  public Animal(string name) {
    this.name = name;
  }

  public string AsString() {
    return this.name;
  }

  public bool AsBoolean() {
    return !String.IsNullOrEmpty(this.name);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

(是的，我知道字符串在技术上不是原语。我觉得那很讨厌。)

然后在另一部分代码:

```
public bool IsCat(Animal animal) {
  return animal && animal == "cat";
} 
```

Enter fullscreen mode Exit fullscreen mode

所以在`animal && animal == "cat"`中，编译器会理解第一个`animal`是`animal.AsBoolean()`的语法糖，第二个`animal`是`animal.AsString()`的语法糖。大概还会有其他用于`byte`、`char`和数字类型的“IAsPrimitive”接口。

以下是我的问题:

1.  有语言有这个吗？我知道 C#允许重载操作符，所以`animal == "cat"`部分是可能的，尽管不是以我描述的方式。
2.  这是个好主意吗？它是否引入了不必要的歧义，或者它只是一块我们将学会识别的语法糖？
3.  用像 JavaScript 这样的松散类型语言做这件事怎么样？能够描述我们希望解释器做的类型转换是一种优势吗，还是会给真正理解 JavaScript 的人带来一大堆问题？

让我知道你的想法。