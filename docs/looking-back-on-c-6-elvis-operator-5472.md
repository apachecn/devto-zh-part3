# 回顾 C# 6: Elvis 运算符

> 原文：<https://dev.to/csmacnz/looking-back-on-c-6-elvis-operator-5472>

[![Looking back on C# 6: Elvis Operator](img/8b9f3c305c469e3cfb1c1889c70b8078.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qhHJG9ui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1519508299351-658aa1d7f3a2%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想介绍一些我一直在使用的 C# 6 和 c# 7 语言的特性，这些特性你可能已经错过了。

我们从 C# 6 的 Elvis 操作符开始。我相信真正的名字是空条件运算符，但我更喜欢前者。

最好先看看这个特性所针对的最简单、最常见的代码场景。

```
var widget = service.GetWidget(widgetId);

if(widget.IsInStock())
{
   service.PurchaseWidgets(widgetId, quantity);
} 
```

购买一些小部件的简单场景。相当标准的代码。

但是如果 id 不正确时`GetWidget`返回`null`怎么办？(返回空值是否是一个好主意是另一个话题，所以我们在这里不深入讨论。)当我们试图检查`widget.IsInStock()`时，我们得到了一个`NullReferenceException`。

我们可以解决这个问题:

```
var widget = service.GetWidget(widgetId);

if(widget != null && widget.IsInStock())
{
   service.PurchaseWidgets(widgetId, quantity);
} 
```

我们添加了一个`null`防护，一切又像预期的那样工作了。对于这些非常常见的用例，代码变得有些冗长。

相反，我们可以使用新的(ish)符号，空条件运算符或“猫王运算符”，使用时看起来像`?.`，斜视时看起来有点像两只眼睛(`..`)和一绺头发，类似于因猫王而出名的[。(从技术上来说，它只是一个`?`，但它通常以一个`.`结尾。)](http://www.schwarzkopf.international/en/hairstyling/fiftieshairstyles/elvis-presley-hairstyle.html)

这个符号是一个空支票。意思是“如果左边计算的表达式为空，不要计算右边，如果需要，计算为空值。”

一些示例用法:

```
interface ITestInterface {
    int ANumberProperty { get; }
    void DoSomeWork();
    string GetSomeData();
    long GetALong();
}

// getValue returns an `ITestInterface`
ITestInterface aValue = getValue();

// Safely get the value from a Property when the object might be null
int? aNumber = aValue?.ANumberProperty;

// if aValue is not null, execute `.DoSomeWork()`
aValue?.DoSomeWork();

// if aValue is not null, execute `.GetSomeData()`
// at this point, data could be null, and as a string, could have been anyway
string data = aValue?.GetSomeData();

// if aValue is not null, execute `.GetALong()`
// at this point, the result is now nullable
// We should all try to get along...
long? aLong = aValue?.GetALong(); 
```

对于我们上面的例子，这意味着我们现在可以这样写:

```
if(widget?.IsInStock() == true)
{
   service.PurchaseWidgets(widgetId, quantity);
} 
```

这是与上面的工作示例功能相同的代码。注意，因为`if(...)`需要一个布尔表达式(计算结果为真或假的东西),所以我们需要在末尾添加`== true`。这是因为`widget?.IsInStock()`评估为`Nullable<bool>`，或者`bool?`(由于 Elvis 操作符，原来的`bool`变成了`bool?`。)个人认为这是非常可读的代码。

另一个理想的场景是函数指针。特别是委托、函数和动作的语言特征。为了实现这一点，您必须使用`Invoke`方法，而不是像执行函数一样执行它们。下面是我的意思的例子。

```
public event PropertyChangedEventHandler PropertyChanged;

private void OnPropertyChanged(string propertyName)
{
    // This is a pretty traditional EventHandler scenario implementation
    var handler = PropertyChanged;
    if (handler != null)
        handler.Invoke(this, new PropertyChangedEventArgs(propertyName));
} 
```

如果这段代码看起来不熟悉，请跳到下一段。否则，我就继续。事件和 EventHandler 代码可能会有问题。如果没有人实际订阅该事件，执行可能会失败。但这就是我们更新使用 Elvis 操作符的地方，并扔掉了一堆代码。您甚至不必创建一个卷影复制变量来避免多线程更新争用情况，因为编译器生成的代码会为您做这件事！

```
private void OnPropertyChanged(string propertyName)
{
    PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
} 
```

对于函数和动作，也可以使用 Elvis 操作符来防止空值。

```
public void DoWork(Action<string> logStuff)
{
    // Work happens here

    // This throws when `logStuff` is null
    // logStuff("Finished Doing Work."); 

    // This will work, only executing if logStuff is not null
    logStuff?.Invoke("Finished Doing Work.");
}

public Stuff GetStuff(Func<string> getMoreStuff)
{
    // Work happens here

    // This also throws when `getMoreStuff` is null
    // var moreStuff = getMoreStuff();

    // moreStuff will be null if getMoreStuff is null, OR if getMoreStuff returns null
    // You still might do a null check if you care about the difference
    var moreStuff = getMoreStuff?.Invoke();

    // We could also use the null coalescing operator (`??`) as well to fallback in both cases:
    var another = getMoreStuff?.Invoke() ?? "FallBack Value";

    return new Stuff
    {
        Thing1 = 3.4,
        Thing2 = 3.3,
        MoreStuff = moreStuff
    };
} 
```

虽然我经常听到人们将“聪明的代码”作为反模式来谈论，并且我完全同意这种说法，但我发现任何特定特性或技术“聪明”的论点都是非常时间敏感的。当一种语言特性达到足够普遍的使用状态时，它就不再被认为是“聪明”的了。(与两年前相比，这方面的历史例子可能是`foreach`扩展方法、lambdas，甚至 LINQ 语句和异步。)

这也适用于这里。是时候在您的代码中包含 Elvis 操作符了。