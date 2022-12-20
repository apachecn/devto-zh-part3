# 多态性到底是什么？

> 原文：<https://dev.to/jvanbruegge/what-the-heck-is-polymorphism-nmh>

多态性通常是定义数据结构或算法的思想，因此您可以将它们用于多种数据类型。不过，完整的答案有点微妙。在这里，我收集了各种形式的多态性，从您最可能已经使用的常见类型到不太常见的类型，并比较了它们在面向对象或函数式语言中的表现。

## 参数多态性

在许多语言中，这是一种非常常见的技术，尽管更广为人知的名称是“泛型”。核心思想是允许程序员在定义以后可以用任何类型填充的数据结构时使用通配符类型。例如，这在 Java 中是这样的:

```
class List<T> {
    class Node<T> {
        T data;
        Node<T> next;
    }

    public Node<T> head;

    public void pushFront(T data) { /* ... */ }
} 
```

`T`是类型变量，因为您可以稍后“分配”您想要的任何类型:

```
List<String> myNumberList = new List<String>();
myNumberList.pushFront("foo");
myNumberList.pushFront(8) // Error: 8 is not a string 
```

这里的列表只能包含 string 类型的元素，不能包含其他元素。如果我们试图违反这一点，就会得到有用的编译器错误。此外，我们不必再次为每个可能的数据类型定义列表，因为我们可以为所有可能的类型定义列表。

但是，不仅命令式语言或面向对象语言具有参数多态性，它在函数式编程中也很常见。例如在 Haskell 中，列表是这样定义的:

```
data List a = Nil | Cons a (List a) 
```

这个定义意味着:一个列表接受一个类型参数`a`(等号左边的所有内容定义了类型)，它要么是一个空列表(`Nil`)，要么是一个类型为`a`的元素和一个类型为`List a`的列表。我们不需要任何外部的`pushFront`方法，因为第二个构造函数已经这样做了:

```
let emptyList = Nil
    oneElementList = Cons "foo" emptyList
    twoElementList = Cons 8 oneElementList -- Error: 8 is not a string 
```

## 即席多态性

这通常被称为函数或运算符重载。在允许这样做的语言中，您可以多次定义一个函数来处理不同的输入类型。例如在 Java 中:

```
class Printer {
    public String prettyPrint(int x) { /* ... */ }
    public String prettyPrint(char c) { /* ... */ }
} 
```

编译器会根据您传递给它的数据类型自动选择正确的方法。这可以使 API 更容易使用，因为您可以用任何类型调用相同的函数，而不必记住不同类型的一堆变体(例如`print_string`、`print_int`等)。

在 Haskell 中，特别多态通过类型类工作。类型类有点像面向对象语言中的接口。请看这里的例子同样漂亮的打印机:

```
class Printer p where
    prettyPrint :: p -> String

instance Printer Int where
    prettyPrint x = -- ...

instance Printer Char where
    prettyPrint c = -- ... 
```

## 亚型多态性

子类型更好地被称为面向对象的继承。典型的例子是车辆类型，这里是 Java:

```
abstract class Vehicle {
    abstract double getWeight();
}

class Car extends Vehicle {
    double getWeight() { return 10.0; }
}

class Truck extends Vehicle {
    double getWeight() { return 100.0; }
}

class Toyota extends Car { /* ... */ }

static void printWeight(Vehicle v) {
    // Allowed because all vehicles have to have this method
    System.out.println(v.getWeight()); 
} 
```

这里我们可以使用*的**任何**子类，就像*是一个直接的车辆类一样。请注意，我们不能走另一条路，因为不是每辆车都保证是汽车。

当您被允许传递函数时，这种关系就变得有点棘手，例如在 Typescript:

```
const driveToyota = (c: Toyota) => { /* ... */ };
const driveVehicle = (c: Vehicle) => { /* ... */ };

function driveThis(f: (c: Car) => void): void { /* ... */ } 
```

这两个函数中的哪一个允许传递给`driveThis`？你可能会想到第一个，毕竟正如我们在上面看到的，一个期望一个对象的函数也可以被传递给它的子类(参见`printWeight`方法)。**但是**如果你传递一个*函数*这是错误的。你可以这样想:`driveThis`想要能接受**任何**车的东西。但如果传入`driveToyota`，功能只能对付丰田不够用。另一方面，如果你传入一个可以驱动任何*车辆* ( `driveVehicle`)的函数，这也包括汽车，所以`driveThis`会接受它。

由于 Haskell 不是面向对象的，所以子类型没有多大意义。

## 行多态性

现在我们来看看不常用的多态类型，它们只在很少的语言中实现。

行多态性就像子类型化的小兄弟。我们允许指定一个行扩展:`{ a :: A | r }`，而不是说`{ a :: A, b :: B }` **形式的每个对象也是**形式的`{ a :: A }`。这使得在函数中使用更容易，因为您不必考虑是否允许传递更具体或更一般的类型，而是只需检查您的类型是否与模式匹配。所以`{ a :: A, b :: B }`与`{ a :: A | r }`匹配，而`{ b :: B, c :: C}`不匹配。这也有一个好处，就是你不会丢失信息。如果你把一个`Car`投射到一个`Vehicle`上，你就失去了这个物体是哪辆车的信息。使用行扩展，您可以保存所有信息。

```
printX :: { x :: Int | r } -> String
printX rec = show rec.x

printY :: { y :: Int | r } -> String
printY rec = show rec.y

-- type is inferred as `{x :: Int, y :: Int | r } -> String`
printBoth rec = printX rec ++ printY rec 
```

实现行多态性的最流行的语言之一是 PureScript，我也是 T2，目前正致力于将它引入 Haskell。

## 种多态性

种类是类型的类型。我们都知道值，它是每个函数都要处理的数据。`5`、`"foo"`、`false`都是数值的例子。然后是类型层，描述值。这个应该也是程序员非常了解的。之前三个值的类型是`Int`、`String`和`Bool`。但在此之上还有一个层次:种类。所有类型的种类是`Type`也写作`*`。所以这表示`5 :: Int :: Type` ( `::`表示“有类型”)。还有其他种类。例如，我们前面的列表类型是一个类型(`List a`)，那么什么是`List`(没有`a`)？它仍然需要另一个类型作为参数来构成一个普通类型。故其种类为`List :: Type -> Type`。如果你给`List`另一个类型(例如`Int`)，你会得到一个新的类型(`List Int`)。

种类多态性是指一种类型只能定义一次，但仍可用于多种类型。最好的例子是 Haskell 中的`Proxy`数据类型。它用于“标记”一个类型为
的值

```
data Proxy a = ProxyValue

let proxy1 = (ProxyValue :: Proxy Int) -- a has kind `Type`
let proxy2 = (ProxyValue :: Proxy List) -- a has kind `Type -> Type` 
```

## 高阶多态

有时，正常的特定多态性是不够的。有了 ad-hoc 多态，你可以为不同的类型提供许多实现，API 的消费者可以选择他想要使用的类型。但是有时候你作为一个 API 的生产者想要选择你想要使用的实现。这就是你需要高阶多态的地方。在 Haskell 中是这样的:

```
-- ad-hoc polymorphism
f1 :: forall a. MyTypeClass a => a -> String
f1 = -- ...

-- higher-rank polymorphism
f2 :: Int -> (forall a. MyTypeClass a => a -> String) -> Int
f2 = -- ... 
```

我们没有把`forall`放在最外面，而是把它向内推，因此声明:给我一个函数，它可以处理任何实现`MyTypeClass`的类型`a`。你大概能看到`f1`就是这样一个函数，所以允许你把它传递给`f2`。

为什么这是有用的标准例子是所谓的“ST-Trick”。正是这一点使得 Haskell 拥有不可逃脱的可变状态:

```
doSomething :: ST s Int
doSomething = do
    ref <- newSTRef 10
    x <- readSTRef ref
    writeSTRef (x + 7)
    readSTRef ref 
```

这里的`s`参数很重要，每个有状态操作在签名中都需要相同的`s`。神奇的是，现在这个函数可以将有状态计算转换成纯粹的计算，`runST` :

```
runST :: forall a. (forall s. ST s a) -> a 
```

您可以看到,`s`是使用更高级的多态性创建的。由于我们没有指定对*的任何约束，有状态计算除了在类型签名中传递它之外，不能对它做任何事情。它的行为就像`Proxy`的“标签”。因为它只在有状态动作的范围内定义(由内部的`forall`)，所以每次试图从计算中泄漏任何东西都是编译器中的一个错误。*

## 线性多态性

线性多态性与线性类型有关，也就是跟踪数据“使用”的类型。线性类型跟踪一些数据的所谓多重性。一般来说，你可以区分三种不同的多重性:“零”，只存在于类型层，不允许在值层使用；“一”表示不允许复制的数据(文件描述符就是一个例子)，而“多”表示所有其他数据。

线性类型有助于保证资源的使用。例如，如果你在函数式语言中使用一个可变数组。通常你必须在每一步复制数组，或者你必须使用低级的不安全函数。但是对于线性类型，你可以保证这个数组一次只能在一个地方使用，所以不会发生数据竞争。

多态性方面在类似前面提到的`fold`的函数中发挥了作用。如果你给 fold 一个只使用一次参数的函数，那么整个`fold`将只使用一次初始值。如果您传递一个多次使用该参数的函数，初始值也将被多次使用。线性多态性允许您只定义一次函数，并且仍然提供这种保证。

线性类型类似于 Rust 的借用检查器，但是 Rust 并没有真正的线性多态。Haskell 很快就会得到线性类型和多态。

## 轻浮多形性

在 Haskell 中，所有普通的数据类型都只是对堆的引用，就像在 Python 或 Java 中一样。但是 Haskell 也允许使用所谓的“非提升”类型，例如直接使用机器整数。这意味着 Haskell 实际上在类型级别上编码了数据类型的内存布局和位置(堆栈或堆)!这些可以用来进一步优化代码，因此 CPU 不必先加载引用，然后从(慢)RAM 请求数据。

当你定义的函数既作用于提升类型也作用于非提升类型时，就是易变多态。