# 类型

> 原文：<https://dev.to/gillchristian/day-13-types-59m6>

> 有很多方法可以理解程序。人们常常过于依赖一种方法，这种方法被称为“调试”，包括运行一个部分理解的程序，看看它是否能达到你的预期。ML 提倡的另一种方法是在程序本身中安装一些理解的手段。
> 
> 罗宾·米尔纳

我已经中断 100 天计划太久了，部分是因为我很忙，部分是因为我很懒。从现在开始，我会尽量保持不变。

本周在代尔夫特哈斯克尔学习小组中，我们分别从基本原理、“基本数据类型”和“类型”开始，复习了[哈斯克尔编程的第四章和第五章。](http://haskellbook.com/)

作为最后一个，这只是我的笔记和书中的一些引用，作为本章的总结。

## 基本数据类型

> Haskell 有一个健壮且富于表现力的类型系统。类型在 Haskell 代码的可读性、安全性和可维护性方面起着重要的作用，因为它们允许我们对数据进行分类和定界，从而限制了我们的程序可以处理的数据形式。类型，也称为数据类型，提供了一种更快速地构建程序的方法，同时也更易于维护。

和(或并)类型(析取):

```
data Bool = False | True 
```

产品类型(连词):

```
data (,) a b = (,) a b

data (,,) a b c = (,) a b c

-- ... 
```

列表:

```
data [] a = [] | a : [a] 
```

数字类型:

*   `Int`
*   `Integer`
*   `Float`
*   `Double`
*   `Rational`
*   `Scientific`

```
λ> :i Num
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a

λ> :i Fractional
class Num a => Fractional a where
  (/) :: a -> a -> a
  recip :: a -> a
  fromRational :: Rational -> a 
```

Haskell 中的数据构造函数提供了一种在给定类型中创建值的方法。Haskell 中的数据构造函数有一个类型，可以是常量值(nullary ),也可以像函数一样接受一个或多个参数。

Haskell 中的**类型构造函数**是*而不是*值，只能在类型签名中使用。正如数据声明生成数据构造函数来创建驻留在该类型中的值一样，数据声明生成类型构造函数来表示该类型。

## 类型

Haskell 基于[系统 F](https://en.wikipedia.org/wiki/System_F) 和[亨德利-米尔纳](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system)型系统，在上面做了一些改进。

**类型是什么？**

逻辑和数学中的类型系统被设计来施加约束，以加强正确性。

类型系统定义了程序不同部分之间的关联，并检查所有部分是否以逻辑一致、可证明正确的方式组合在一起。

**打字签名**

```
'f' :: Char
"foo" :: String -- or [Char]
True :: Bool

not :: Bool -> Bool -- function from Bool to Bool
fst :: (a, b) -> a  -- get a tuple's first value

(+) :: Num a => a -> a -> a -- type class constraint 
```

箭头(`->`)是类型构造函数。

```
λ> :i (->)
data (->) (a :: TYPE q) (b :: TYPE r)   -- Defined in ‘GHC.Prim’
infixr 0 -> 
```

向右关联(因此`infixr`)。

```
f :: a -> a -> a

-- is the same as

f :: a -> (a -> a)

-- and

map :: (a -> b) -> [a] -> [b]

-- is the same as

map :: (a -> b) -> ([a] -> [b]) 
```

简化的函数构造器可以是`data (->) a b`。

需要注意的一点是，函数只接受一个参数并返回一个结果。输入*curry*。

> Currying 是指多个函数的嵌套，每个函数接受一个参数并返回一个结果，以允许多参数函数的假象。

currying 的好处是*局部应用*。

```
λ> add a b = a + b
λ> addOne = add 1
λ> add 1 2
3
λ> addOne 2
3
λ> 1 + 2
3
λ> add = (+)
λ> add 1 2
3
λ> (+1) 2
3
λ> addOne = (+1)
λ> addOne 2
3 
```

### 多态性

对我来说，Haskell 实现多态性的方式是它的关键特性之一。我不会讲太多细节，只是补充一下这本书的定义，否则会很长。

*polymer*，19 世纪初由希腊语 poly 发明，意为“许多”，morph 意为“形式”。多态中的-ic 后缀表示“制成的”

在 Haskell 中，多态分为两类:*参数多态*和*约束多态*。

**参数性**是在参数多态性存在的情况下保持的属性。参数化表明函数的行为在该函数的所有具体应用中都是一致的。参数化 4 告诉我们函数:

```
id :: a -> a 
```

可以理解为对 Haskell 中的每种类型都有相同的行为，而不需要我们看到它是如何编写的。同样是这个属性告诉我们:

```
const :: a -> b -> a 
```

`const`必须返回第一个值——参数化，类型的定义需要它！

```
f :: a -> a -> a 
```

在这里，`f`只能返回第一个或第二个值，其他什么都不会，而且它会始终一致地返回一个或另一个值，不会改变。如果函数`f`利用了(+)或(*)，那么它的类型必然会受到类型类 Num 的约束，从而成为一个特别的、而不是参数化的多态性的例子。

**特别多态性**(有时称为“受约束的多态性”)是一种多态性，它将一个或多个类型类约束应用于所有具体应用中的行为参数一致性，特别多态性的目的是允许函数在每个实例中具有不同的行为。这种特定性受到定义方法的 type 类中的类型以及 Haskell 要求 type 类实例对于给定类型是唯一的约束。对于任何给定的类型类和类型的组合，如 Ord 和布尔，在范围内必须只存在一个唯一的实例。这使得推理类型类变得相当容易。

```
-- ad-hoc polymorphism via the Num type class
(+) :: Num a => a -> a -> a

-- parametric polymorphism a
c :: a -> a -> a 
```

* * *

这是这本书开始变得有趣的时候。但目前就这样了😄

* * *

免责声明，这篇博文的大部分内容都是引用自第一原理的 [Haskell 编程。我无意剽窃这本书，这些只是第四章和第五章的概述。](http://haskellbook.com/)