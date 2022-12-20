# 还有另一个-闪电快速对象映射器。网

> 原文：<https://dev.to/alexjitbit/yet-another---lightning-fast---object-mapper-for-net-2bj2>

我叫 Alex，我刚刚创建了一个[对象-2-对象映射器](https://github.com/jitbit/PropMapper)。

我知道，已经有很多了。但是仍然...我认为这个世界值得拥有另一个世界的原因是速度。如今，大多数地图绘制者(包括著名的 AutoMapper)至少在某种程度上依赖于反射。而且反射慢。它使用对象的元数据来定位和调用成员，这比直接调用成员大约慢三倍。

跑一两次也没什么大不了的。但是对象映射经常发生在一个`foreach`循环中，这个循环可能有数千次迭代。

## 输入表达式

首先，快速解释一下什么是“表达式”。

从那时起，表情就存在了。NET 4.0 和 C# 3.0。一个`Expression`对象只是一个对可执行代码行的引用。代码可以在设计时显式编写**或在运行时构建**。甚至*编*到 MSIL/CIL 跑得更快。

取一个λ函数:

`Func<int, int, int> add = (x, y) => x + y;`

很常见。让我们进一步把它包装成一个`Expression` :

```
Expression<Func<int, int, int>> addExpression = (x, y) => x + y; 
```

这里的表达式不仅仅是可执行代码，它还是一个包含元信息的数据对象。

```
Console.WriteLine((addExpression as BinaryExpression).Body); 
```

这产生:

```
"(x + y)" 
```

我们刚刚通过编写一些显式代码构建了一个表达式，然后用它创建了一个`Expression`对象。

但是最酷的部分是**这也反过来工作** -你可以先构建一个表达式，然后*将它编译回可执行代码。*

## 使用表达式进行对象映射

您已经看到了如何从实际代码中构建表达式。现在让我们反过来做——让我们从元信息和表达式中构建代码。

假设您有两个`PropertyInfo`对象，它们保存了关于类型属性的元信息。

```
PropertyInfo prop1;
PropertyInfo prop2; 
```

您可以简单地构建一个将一个属性分配给另一个属性的表达式。

```
var e = Expression.Assign(Expression.Property(srcObj, prop1), Expression.Property(destObj, prop2))); 
```

现在创建一个 lambda 函数:

```
var lambda = Expression.Lambda<Action<Type1, Type2>>(e, srcObj, destObj);
var action = lambda.Compile();

//now we can call the "action"
action(object1, object2); //this will run "object1.prop1 = object2.prop2" 
```

## PropMapper

[PropMapper](https://github.com/jitbit/PropMapper) 就是这样构建的。它生成一个表达式树，然后编译并缓存在一个静态变量中。

它确实增加了一点枚举类型属性的开销(仍然非常快)，但是它几乎和手工的“逐个属性”赋值一样快。

**因为是！**

它是一个表达式树，生成代码并编译成 MSIL(以及后来的本机代码)。

运行 100k 次迭代需要 **25 毫秒**(手动映射需要 10ms)

就一个。cs-file，80 行代码(没错，我花了很多时间在 JavaScript 世界中，在那里对文件大小的困扰是一件事)。

点击查看项目(和基准)[。](https://github.com/jitbit/PropMapper)

P.S. `Dapper.NET`使用了类似的方法，但是它更像是一个 ORM 而不是属性映射器，所以如果你只是需要一个简短、简单和扁平的对象映射器——试试吧。