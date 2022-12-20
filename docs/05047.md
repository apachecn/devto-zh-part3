# 回顾 C# 6:初始值设定项

> 原文：<https://dev.to/csmacnz/looking-back-on-c-6-initializers-1h2k>

[![Looking back on C# 6: Initializers](img/767918e364a1f1d2e74b03172385ef0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VC6frUv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1502426899022-660e3522a15a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

自早期 C#时代以来，初始化器已经有了一些升级。但是在我们看更多最近的变化之前，我们将回顾以前发生的事情。

初始值设定项或对象初始值设定项是执行一块内存(结构或对象)的初始设置的代码的名称。构造函数是其中的一部分，但是它的思想更倾向于为您想要设置的所有状态整体设置对象数据。

看看 C 语言的更多背景知识，我们有这样的初始化器:

```
struct MyType { char s; int n; };

// We can initialize a variable using a curly brace syntax:
MyType x = { 'a', 42 }; 
```

我们在上面看到的是，我们正在初始化我们的变量`x`，将它的数据`.s`和`.n`分别设置为`'a'`和`42`。这是初始化。现在用 C#，比较熟悉的东西:

```
public class Foo
{
    public string Name { get; set; }
    public int Number { get; set; }
}

// Without using initializers:
Foo myFoo = new Foo();
myFoo.Name = "a";
myFoo.Number = 42;

// In C# we can initialize in a similar way to C.
// This code compiles to the same instructions as above:
Foo myFoo = new Foo() { Name = "a", Number = 24 };

// Collections also support initialization
int[] myArray = new int[] { 1, 1, 2, 3, 5, 8 }; 
```

我不会深入讨论 C 如何改进它自己的初始化器，这是一个不同的话题。相反，我们将看看 C#是如何在 C# 3 的初始语法之上，在 C# 6 中采用并增强这个简单的初始化器的。

### 增加对收藏的扩展支持

当初始化器被实现的时候(或者不久之后，如果我的记忆欺骗了我),有一个小魔法让它工作。也就是说，编写的代码更简单、更简洁，但是实现变得更熟悉了。

集合初始化也是如此。如果我们回顾上面的例子，我们可以看到:

```
// Collection initialization
int[] myArray = new int[] { 1, 1, 2, 3, 5, 8 };

// The same code, more verbosely:
int[] myArray = new int[];
myArray.Add(1);
myArray.Add(1);
myArray.Add(2);
myArray.Add(3);
myArray.Add(5);
myArray.Add(8); 
```

基本上，语法是对集合调用`Add(`的简写。这允许更复杂的情况，其中`Add`可以接受多个参数。

```
var myDict = new Dictionary<int, string>() { { 1, "One" }, { 2, "Two" } };

// This worked the same way as the last example. The same code is:
var myDict = new Dictionary<int, string>();
myDict.Add(1, "One");
myDict.Add(2, "Two"); 
```

同样，这是所有现有的语法。C# 6 增加了一个功能，这样你就不需要在你的类型上实现`Add`，你也可以创建扩展方法:

```
public static void Add(this Dictionary<int, string> dict, Foo value)
{
    dict.Add(value.Number, value.Name);
}

Foo foo1 = new Foo() { Name = "One", Number = 1 };
Foo foo2 = new Foo() { Name = "Two", Number = 2 };
var myDict = new Dictionary<int, string>() { foo1, foo2 }; 
```

在这一点上，创建你自己的领域特定语言(DSL)的可能性是无限的，并且是完全可定制的。您甚至不必使用集合，因为任何东西都可以通过扩展添加一个`Add`重载。

### 索引初始值设定项

字典语法有点奇怪。Add 并不是真正正确的方法。而且我们已经有了索引器语法:

```
var myDict = new Dictionary<int, string>();
myDict[7] = "seven";
myDict[9] = "nine";
myDict[13] = "thirteen; 
```

为什么不扩展初始化器来使用索引器呢？他们做到了。又是 C# 6。

```
var numbers = new Dictionary<int, string> {
    [7] = "seven",
    [9] = "nine",
    [13] = "thirteen"
}; 
```

按预期工作，结果与上面的代码相同。这基于相同的现有索引器模式(与前面的`Add`用法类似)，因此适用于任何自定义类型的任何自定义索引器。

```
// Using an existing type from Newtonsoft.Json:
var x = new JObject { ["x"] = 3 }; 
```

### 汽车属性初始化器

那是一堆消费端初始化。在 C# 6 中也有更多的幕后添加。

卑微的汽车财产:

```
public int X { get; set; } 
```

首先，我们现在可以给出一个默认的初始化:

```
// This is the same as `X = 4` in the constructor
public int X { get; set; } = 4; 
```

为了使这个技巧更加完整，我们有了 getter-only 属性:

```
public int Y { get; } = 42; 
```

以及从构造函数初始化它的能力:

```
public class MyFoo
{
    public int Y { get; };    

    pubilc MyFoo()
    {
        Y = 42;
    }
} 
```

有时你可能有许多构造函数重载，使用属性初始化器可以减少代码重复。有时您可能希望不同的重载有不同的值，那么您可以在构造函数中初始化。

无论哪种方式，这都给了你真正的权力来控制你的只读权限，而不需要像以前那样写冗长的等价语句:

```
// Same code as above in "full":
public class MyFoo
{
    private readonly int _y;
    public int Y
    {
        get
        {
            return _y;
        }
    };    

    pubilc MyFoo()
    {
        _y = 42;
    }
} 
```

新的方法更简洁，尤其是当有很多属性时:

```
// Just imagine how many lines of code this would be the old way!
public class MyFoo
{
    public string X { get; } = "Foo";    
    public int Y { get; } = 42;    
    public decimal Z { get; } = 54m;
    public object Key { get; } = new object{};

    // Other read/write props
    public int Another { get; set; }
    public bool Widgets { get; set; }
} 
```

### 总结

对象初始化在 C# 6 中有了重大改进。你经常使用多少？您在自己的代码库中注意到了多少？这些已经存在了一段时间，所以只能期待看到更多。帮你自己一个忙，如果你还没有开始使用它。可读性很强，击键次数更少。典范？你打赌！