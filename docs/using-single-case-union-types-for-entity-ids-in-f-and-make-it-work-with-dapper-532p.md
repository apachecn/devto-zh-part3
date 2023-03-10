# 在 F#中对实体 id 使用单例联合类型，并使其与 Dapper 一起工作

> 原文：<https://dev.to/t4rzsan/using-single-case-union-types-for-entity-ids-in-f-and-make-it-work-with-dapper-532p>

在 C#中，使用强类型实体 id 而不是在实体上使用整数或类似的 id 是一种常见和流行的模式。当试图防止将订单 ID 与订单行 ID 混淆时，强类型实体 ID 非常有用。

安德鲁·洛克最近写了一个[系列](https://andrewlock.net/using-strongly-typed-entity-ids-to-avoid-primitive-obsession-part-1/)，主题是你现在应该去读一读。

强类型实体 ID 可能看起来像这样(Andrew Lock 使用的例子):

```
public readonly struct OrderId : IComparable<OrderId>, IEquatable<OrderId>
{
    public int Value { get; }

    public OrderId(int value)
    {
        Value = value;
    }

    public bool Equals(OrderId other) => this.Value.Equals(other.Value);
    public int CompareTo(OrderId other) => Value.CompareTo(other.Value);

    public override bool Equals(object obj)
    {
        if (ReferenceEquals(null, obj)) return false;
        return obj is OrderId other && Equals(other);
    }

    public override int GetHashCode() => Value.GetHashCode();
    public override string ToString() => Value.ToString();

    public static bool operator ==(OrderId a, OrderId b) => a.CompareTo(b) == 0;
    public static bool operator !=(OrderId a, OrderId b) => !(a == b);
} 
```

Enter fullscreen mode Exit fullscreen mode

这需要为您的域模型中的每个 ID 编写大量的代码，您应该在 Visual Studio 中创建一个代码片段来完成这项工作。然而，和许多东西一样，你可以在 F#中用[单例联合类型](https://fsharpforfunandprofit.com/posts/designing-with-types-single-case-dus/)或多或少地免费得到这个。只需要这样:

```
type CarID = CarID of int 
```

Enter fullscreen mode Exit fullscreen mode

F#会免费给你相等的运算符和其他东西，因此:

```
let id1 = (CarID 42)
let id2 = (CarID 42)

id1 = id2 // true 
```

Enter fullscreen mode Exit fullscreen mode

正如 Scott Wlaschin 在传奇的 [F#上指出的，为了乐趣和利益](https://fsharpforfunandprofit.com/posts/designing-with-types-single-case-dus/)你可以在任何事情上使用单例联合类型，而不仅仅是 id:

```
module Domain =
    type CarID = CarID of int
    type CarMake = CarMake of string
    type Model = Model of string

    type Car = {
        CarID: CarID;
        Make: CarMake;
        Year: int;
        Model: Model;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我并不是说这总是一个好主意，但是不要把它当作一个例子。

如果你试图用 Dapper 从数据库中读取一个`Car`会发生什么？

## 单格工会类型和衣冠楚楚

Dapper 通过调用适当的构造函数或设置属性，使用反射来创建实体类型的实例。对于 F#记录类型，您不能设置属性，因此 Dapper 在查询数据库时会尝试调用构造函数:

```
use cn = getConnection ()
let cars = cn.Query<Car>("SELECT CarID, Make, Year, Model FROM Car") 
```

Enter fullscreen mode Exit fullscreen mode

这将会失败，因为 Dapper 试图用签名`(int * string * int * string)`在`OrderLine`上找到一个构造函数。

为了帮助 Dapper 从`int`转换到`CarID`，从`string`转换到`Make`等等，你可以向 Dapper 注册一个类型处理程序。类型处理程序是继承了`SqlMapper.TypeHandler<>`的类。为了从数据库中读取`CarID`，类型处理程序应该是这样的。

```
type CarIDTypeHandler() =
        inherit SqlMapper.TypeHandler<CarID>()

        override x.SetValue(parameter: IDbDataParameter, value: CarID) =
            ()

        override x.Parse(value: obj) = 
            (CarID (Convert.ToInt32(value))) 
```

Enter fullscreen mode Exit fullscreen mode

用`SqlMapper.AddTypeHandler(typeof<CarID>, new CarIDTypeHandler())`向 Dapper 注册类型处理程序。为您想到的每一个单个 case union 类型编写类型处理程序很快就会变得令人厌烦(很抱歉这个奇怪的句子)，幸运的是，有一种方法可以使用反射为单个 case union 类型编写通用类型处理程序。

```
type SingleCaseUnionTypeHandler<'T>() =
        inherit SqlMapper.TypeHandler<'T>()

        override x.SetValue(parameter: IDbDataParameter, value: 'T) =
            ()

        override x.Parse(value: obj) =
            let cases = FSharpType.GetUnionCases(typedefof<'T>)
            FSharpValue.MakeUnion(cases.[0], [| value |]) :?> 'T 
```

Enter fullscreen mode Exit fullscreen mode

最后一行利用`FSharpValue.MakeUnion`创建一个联合。当使用`cases.[0]`时，它假设联合类型`'T`有且只有一种情况。我还没有找到将泛型类型参数限制为联合的方法。

现在，您可以为所有单例联合类型注册类型处理程序:

```
SqlMapper.AddTypeHandler(typeof<CarID>, new SingleCaseUnionTypeHandler<CarID>())
SqlMapper.AddTypeHandler(typeof<CarMake>, new SingleCaseUnionTypeHandler<CarMake>())
SqlMapper.AddTypeHandler(typeof<Model>, new SingleCaseUnionTypeHandler<Model>()) 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭

上面我们已经看到了如何轻松地使用单例联合类型对实体 id 和其他值进行类型安全处理，以防止它们互换使用。我们还为单例联合类型创建了一个通用的 Dapper 类型处理程序。

泛型类型处理程序仅适用于创建单个联合值类型时不需要额外的逻辑或验证的情况，并且在需要处理大量实例的情况下，当涉及到反射的性能问题时，也应该小心。