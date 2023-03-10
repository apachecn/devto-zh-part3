# 回顾 C# 7:局部函数

> 原文：<https://dev.to/csmacnz/looking-back-on-c-7-local-functions-2715>

[![Looking Back on C# 7: Local functions](img/4566ebb14f8e59bb44e657ef6b795806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TI-DMmkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530226406379-f84b9edd291b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

## Lambdas - a 重述

在 C# 3 中，我们得到了 lambdas。这些是匿名函数，可以作为函数指针传递。

“匿名”是指函数没有名字，并且不依赖于实际的类或实例。这个术语来自函数式编程。

就实现细节而言，有两类 lambdas，一类是独立的纯函数，另一类是有捕获范围的，称为闭包。同样，闭包是一个函数式编程术语。我们从父类的作用域中捕获变量，并将它们封装到这个匿名函数实例中。

纯函数可以非常容易地重构为静态类中的公共静态函数，编译也非常类似。

```
 static Action<int> GetRandomValueFunction()
{
    Console.WriteLine("Use static value for counter: {0}", counter);
    return () => 5;
}

// These two methods are the same as the above
static Action<int> GetRandomValueFunction()
{
    Console.WriteLine("Use static value for counter: {0}", counter);
    return StaticValueFunction;
}

static int StaticValueFunction()
{
    return 5;
} 
```

闭包没有简单的等价构造。希望通过例子，我们可以看到这些如何转化成简单的静态方法。(在一个例子中从[所以](https://stackoverflow.com/a/5438331/2118268) )

```
 static Action CreateShowAndIncrementAction()
{
    Random rng = new Random();
    int counter = rng.Next(10);
    Console.WriteLine("Initial value for counter: {0}", counter);
    return () =>
    {
        Console.WriteLine(counter);
        counter++;
    };
} 
```

给定上面的 C#代码，编译后的代码将更类似于下面的代码:

```
 static Action CreateShowAndIncrementAction()
{
    ActionHelper helper = new ActionHelper();        
    Random rng = new Random();
    helper.counter = rng.Next(10);
    Console.WriteLine("Initial value for counter: {0}", helper.counter);

    // 
    return helper.DoAction;
}

class ActionHelper
{
    public int counter;

    public void DoAction()
    {
        Console.WriteLine(counter);
        counter++;
    }
} 
```

因为编译器生成这些，所以它也控制访问和可见性，所以您实际上不能以这种方式从您的代码直接访问它。但是它会自动为您完成所有这些工作，并且用更少的代码行来实现相同的逻辑执行。从 lambda 版本中也更加明确了调用方法是函数的所有者，其他任何人都不能也不应该共享它。

显然，lambdas 减少了我们的代码行，并且更优越。

## 使用 lambdas

不过，有时候你的小羊羔会变得很复杂。当你在 LINQ 的管道里有大型复杂的兰姆达斯时，它会很快变得混乱。

```
public List<Widget> GetWidgetsByPlumbob(string plumb, int restrictionNumber)
{
    var restriction = _store.GetRestriction(restrictionNumber);

    // This `where` clause is only going to get more complicated from here.
    _store
        .GetWidgets()
        .Where(w => (w.PlumbBob.StartsWith(plumbob) || w.PlumbBob.EndsWith(plumbob)) && w.Restriction == restriction)
        .Select(Map)
        .ToList();
} 
```

在 C# 7 之前，我们有几个选项:

```
public List<Widget> GetWidgetsByPlumbob(string plumb, int restrictionNumber)
{
    var restriction = _store.GetRestriction(restrictionNumber);

    _store
        .GetWidgets()
        .Where(w => Filter(w, plumb, restriction))
        .Select(Map)
        .ToList();
}

private bool Filter(Widget widget, string plumb, Restriction restriction)
{
    return (w.PlumbBob.StartsWith(plumbob) || w.PlumbBob.EndsWith(plumbob)) && w.Restriction > restriction;
} 
```

这有几个问题。我们现在将所有参数传递给嵌套函数。这很好，但是从维护的角度来看，我们可能会添加更多的参数，现在我们每次更改都会维护两个签名。

此外，我们打开另一个函数，开始使用“可共享的”过滤函数。现在我们将耦合到这个新功能。我们不能在不影响其他新功能的情况下改变我们自己的过滤器，这增加了易碎性。有时，复制有不同原因需要更改的逻辑是值得的，但是这段代码的架构并不能防止这一点。

再比如把 lambda 拉进一个变量:

```
public List<Widget> GetWidgetsByPlumbob(string plumb, int restrictionNumber)
{
    var restriction = _store.GetRestriction(restrictionNumber);

    var filter = w => (w.PlumbBob.StartsWith(plumbob) || w.PlumbBob.EndsWith(plumbob)) && w.Restriction == restriction);

    _store
        .GetWidgets()
        .Where(filter)
        .Select(Map)
        .ToList();
} 
```

看看这个例子，它增加了一些简化。因为像最初的例子一样，这个 lambda 是一个闭包，lambda 可以匹配`Where` LINQ 扩展的预期签名。

但是，有时这种方法会导致类型内插问题。也就是如果拉进一个变量，往往不能使用`var`或者需要添加显式类型强制转换来帮助编译器出，否则就编译不出来。

我认为我们实际上并没有像我们希望的那样提高可读性。在第一次读取函数时，可能看不出这是一个 lambda，而不是一个语句。这也是一个简单的例子，他们可以很快变得更复杂。

## 把函数放在你的函数里面！

在 C# 7 中，我们现在可以使用嵌套函数了。这给了我们不污染类的名称空间的好处，同时也使它更具可读性。这也更清楚地表明，函数是由作为唯一消费者的调用者拥有的。

```
public List<Widget> GetWidgetsByPlumbob(string plumb, int restrictionNumber)
{
    var restriction = _store.GetRestriction(restrictionNumber);

    bool Filter(Widget widget)
    {
        return (w.PlumbBob.StartsWith(plumb) || w.PlumbBob.EndsWith(plumb)) && w.Restriction > restriction;
    }

    _store
        .GetWidgets()
        .Where(Filter)
        .Select(Map)
        .ToList();
} 
```

我们的编译器现在确保没有其他人可以使用这个函数。它只能从该方法中调用，并让读者知道这只是该函数的特定实现细节，而不是共享的公共逻辑。(封装。)

在本例中，它还允许我们使用简化的`Where`调用。

这种方法真正有用的最好例子是递归。

通常递归算法有一个引导函数，然后调用递归部分。让我们打印一个带有缩进的项目树。

```
public static void PrintLines(TextWriter out, Tree items)
{
    out.WriteLine(items.Title);
    foreach(var node in items.Children)
    {
        Print(out, node, "");
    }
}

private void Print(TextWriter out, TreeNode node, string indent)
{
    out.WriteLine("{0}{1}", indent, items.Title);
    if(node.HasChildren)
    {
        foreach(var node in items.Children)
        {
            Print(out, node indent + " ");
        }
    }
} 
```

为了简单起见，你现在可以这样写:

```
public static void PrintLines(TextWriter out, Tree items)
{
    void Print(TreeNode node, string indent)
    {
        out.WriteLine("{0}{1}", indent, items.Title);
        if(node.HasChildren)
        {
            foreach(var node in items.Children)
            {
                Print(out, node indent + " ");
            }
        }
    }

    out.WriteLine(items.Title);
    foreach(var node in items.Children)
    {
        Print(node, "");
    }
} 
```

这个例子可能不会减少很多代码行，但是当在一个有更多服务方法的类中时，封装的认知负荷会非常有益。

## 总结

老生常谈的“工具箱中的另一个工具”浮现在脑海中，但这确实是，并且战略性地散布在代码中确实有助于可读性和可维护性。不是“经常使用”,但肯定是我可以并且已经在我的网络应用中使用的东西。