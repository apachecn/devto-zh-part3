# 类型类别

> 原文：<https://dev.to/gillchristian/day-14-type-classes-5571>

下面我们来看一下来自[代尔夫特哈斯克尔学习小组第四次会议](https://www.meetup.com/Delft-Haskell-Study-Group/events/259694156/)的[哈斯克尔书](http://haskellbook.com/)的另一章。这次是第 6 章，关于类型类。

和往常一样，引用来自书中，并意味着一个重述。

## 类型类？

> 在某种意义上，Haskell 中的类型类和类型是对立的。一个类型的声明定义了该类型是如何被创建的，一个类型类的声明定义了一组类型是如何被消费或者在计算中使用的。
> 
> 类型类允许我们概括一组类型，以便为这些类型定义和执行一组标准的特性。

类型类是一种可以在几种类型上工作的接口。通过类型类 Haskell 实现了受约束的或*特设的*多态性。

> 目标是通过事例来定义数据类型，其中用户可以向数据类型添加新事例，并在数据类型上添加新函数，而无需重新编译现有代码，同时保持静态类型安全(例如，没有强制转换)。 <sup id="fnref1">[1](#fn1)</sup>

所以我们有类型类作为约束或规则，我们说类型通过实现或实例化那些约束/规则来实现或实例化类型类。

例如，这就是`Bool`类型所实现的。

```
λ> :i Bool
data Bool = False | True        -- Defined in ‘GHC.Types’
instance Eq Bool -- Defined in ‘GHC.Classes’
instance Ord Bool -- Defined in ‘GHC.Classes’
instance Show Bool -- Defined in ‘GHC.Show’
instance Read Bool -- Defined in ‘GHC.Read’
instance Enum Bool -- Defined in ‘GHC.Enum’
instance Bounded Bool -- Defined in ‘GHC.Enum’ 
```

这意味着为`Eq`、`Ord`、`Show`、`Read`、`Enum`和`Bounded`定义的所有约束都可以与`Bool` s 一起使用

我们来看看`Ord`

```
λ> :i Ord
class Eq a => Ord a where
  compare :: a -> a -> Ordering
  (<) :: a -> a -> Bool
  (<=) :: a -> a -> Bool
  (>) :: a -> a -> Bool
  (>=) :: a -> a -> Bool
  max :: a -> a -> a
  min :: a -> a -> a
  {-# MINIMAL compare | (<=) #-}
-- ... 
```

我们可以看到它定义了所有与排序相关的操作，以及为了实例化类型类而需要定义的最小操作集。

它也受`Eq`的约束，类型类用于相等。这很有意义，如果不知道它们何时相等或不相等，我们怎么能排序呢。

```
λ> :i Eq
class Eq a where
  (==) :: a -> a -> Bool
  (/=) :: a -> a -> Bool
  {-# MINIMAL (==) | (/=) #-}
-- ... 
```

根据这个定义，我们可以看到约束。所有的`a`都不是完全多态的，它们受到类型类的约束(`Eq`或`Ord`)。

```
λ> :t (==)
(==) :: Eq a => a -> a -> Bool 
```

它可以是实现 type 类的任何类型。

一旦我们应用一个论点，它就变得更加具体

```
λ> :t (==) 1
(==) 1 :: (Eq a, Num a) => a -> Bool
Prelude
λ> :t (==) (1 :: Int)
(==) (1 :: Int) :: Int -> Bool 
```

## 写作类型类

```
data DayOfWeek
  = Mon
  | Tue
  | Wed
  | Thu
  | Fri
  | Sat
  | Sun

data Date =
  Date DayOfWeek Int

instance Eq DayOfWeek where
  (==) Mon Mon = True
  (==) Tue Tue = True
  (==) Wed Wed = True
  (==) Thu Thu = True
  (==) Fri Fri = True
  (==) Sat Sat = True
  (==) Sun Sun = True
  (==) _ _ = False

instance Eq Date where
  (==) (Date weekday dayOfMonth)
       (Date weekday' dayOfMonth') =
    weekday == weekday' && dayOfMonth == dayOfMonth' 
```

实现类型类非常简单，我们必须为我们的类型定义所需的函数(规则),就是这样。

当编写带有多态参数的类型的实例时(例如示例中的`Date`),我们需要确保这些参数也受到约束。

以下内容会导致编译错误。

```
data Identity a =
  Identity a

instance Eq (Identity a) where
  (==) (Identity v) (Identity v') = v == v' 
```

它不起作用，因为`a`是完全多态的(即不受约束)，但是为了比较它，它需要实现`Eq`。

```
instance Eq a => Eq (Identity a) where
  (==) (Identity v) (Identity v') = v == v' 
```

使`a`受到`Eq`的约束解决了这个问题💪

我们还可以添加`Ord`作为约束，因为它也需要`Eq` :

```
instance Ord a => Eq (Identity a) where
  (==) (Identity v) (Identity v') = v == v' 
```

尽管这可能行得通，但要求超过需要并不好。因为我们没有使用任何`Ord`函数，所以最好使用`Eq`。

对于一些类型类，我们不需要自己编写它们，编译器足够聪明来派生它们。

```
data DayOfWeek
  = Mon
  | Tue
  | Wed
  | Thu
  | Fri
  | Sat
  | Sun
  deriving (Eq, Show) 
```

## 按类型派遣

> 类型类由所有实例将提供的一组操作和值定义。类型类实例是类型类和类型的唯一配对。它们定义了实现该类型的类型类方法的方式。
> 
> 类定义没有定义我们可以编译和执行的任何术语或代码，只定义了类型。代码存在于实例中。

这意味着编译器需要知道由类型类定义的操作中涉及哪些类型，然后使用由这些类型定义的实现。Haskell 偷懒会尽可能的拖延这个。

## 结论

类型类是 Haskell 实现多态性的方法，也是它的杀手锏之一。它不仅避免了[继承问题](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem)！🎉但是类型类是一种非常强大的方式，可以将操作约束到某些类型，而不用将类型耦合到操作(就像对象那样)。

这一章提到了更多关于类型类的东西，但是这应该已经给出了一个很好的关于什么是类型类的实用概念。

附:不知道为什么选了披萨图，现在你也饿了。

* * *

1.  菲利普·瓦德勒，《表达问题》[http://home pages . INF . ed . AC . uk/瓦德勒/papers/expression/expression . txt](http://homepages.inf.ed.ac.uk/wadler/papers/expression/expression.txt)↩