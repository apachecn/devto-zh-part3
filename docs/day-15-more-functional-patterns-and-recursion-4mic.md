# 更多的函数模式和递归

> 原文：<https://dev.to/gillchristian/day-15-more-functional-patterns-and-recursion-4mic>

> 我希望能够永远...把事情分成尽可能多的块，每一块我都单独理解。我想知道把东西加起来的方法，不管我加的是什么。杰拉尔德·苏斯曼

我正在赶进度，因为这与前一周的会议[代尔夫特哈斯克尔学习小组#5](https://www.meetup.com/Delft-Haskell-Study-Group/events/259979758/) 相对应。

和往常一样，我引用《T1》这本书作为总结。

## 功能模式

### 自变量和参数

所有 Haskell 值都可以是函数的参数，甚至是其他函数的参数。这是函数式编程(FP)的关键。可以用作函数参数的值是一个第一级值。

**参数**是函数定义中声明的变量。

```
add :: Num a => a -> a -> a
add a b = a + b
    ^ ^
    | |
   a & b are parameters 
```

**参数**是调用函数时应用于该函数的值。参数的值被绑定到函数定义中的命名参数。

```
x :: Int
x = 1

y :: Int
y = 2

add x y
    ^ ^
    | |
   x & y ar arguments, bound to add's a & b respectively 
```

应用函数会将其参数绑定到值。类型参数绑定到类型，函数变量绑定到值。

### 模式匹配

Haskell 的杀手锏之一就是❤️，每当我在某种程度上支持它的语言中做一些事情，然后返回到 JS/TS 时，我会很痛苦😭

...是一种将值与模式进行匹配的方式，并且在适当的情况下，将变量绑定到成功的匹配。这里值得注意的是，模式可以包括各种各样的东西，如未定义的变量、数字文字和列表语法。

模式匹配允许您在函数定义中公开数据并基于该数据调度不同的行为，方法是通过解构值来公开它们的内部工作方式。

**模式匹配值**

```
isItA :: Char -> Bool
isItA 'a' = True
isItA _   = False

isIt123 :: Int -> Bool
isIt123 1 = True
isIt123 2 = True
isIt123 3 = True
isIt123 _ = False 
```

下划线充当通配符，它匹配任何内容。既然它匹配任何东西，它必须是最后一个案例，否则我们永远不会通过它。

**针对数据构造器的模式匹配**

我们可以在模式匹配中解构数据并与之匹配:

```
withDefault :: a -> Maybe a -> a
withDefault _ Just a  = a
withDefault b Nothing = b

head' :: [a] -> Maybe a
head' []     = Nothing
head' (x:xs) = Just x

first :: (a, b) -> a
first    (a, b) =  a -- notice how much it looks like the signature 
```

**案例表达**

我们还可以在 case 表达式中进行模式匹配，这类似于命令式语言中的`switch` es。但是，顾名思义，它们是表达式。

```
withDefault :: a -> Maybe a -> a
withDefault def maybeA =
  case maybeA of
    Nothing -> b
    Just a  -> a 
```

### 高阶函数(HOFs)

接受函数作为参数的函数。正如我所说的，这是 FP 的关键，允许有效地组合功能。

```
flip :: (a -> b -> c) -> b -> a -> c
flip f b a = f a b 
```

`f`是函数，使`flip`成为 HOF。

这本书给出了更多的例子。但实际上，这是一个在 FP 中随处可见的简单概念，不用花太多心思就能理解。

### 守卫

仅仅是一个语法模式，它依靠真值来决定两个或更多可能的返回结果

```
abs' :: Int -> Int
abs' x
  | x < 0 = (-x)
  | otherwise = x 
```

`otherwise`是守卫的通配符，无非是`True`。

```
λ> :t otherwise
otherwise :: Bool
λ> otherwise
True 
```

### 功能组成

FP 的另一个支柱。允许我们组合函数，以便将应用一个函数的结果作为参数传递给下一个函数。

撰写被定义为点(`.`)运算符:

```
(.) :: (b -> c) -> (a -> b) -> a -> c
(f . g) x = f (g x) 
```

```
λ> :t negate
negate :: Num a => a -> a
λ> :t sum
sum :: (Foldable t, Num a) => t a -> a
λ> :t negate . sum
negate . sum :: (Num c, Foldable t) => t c -> c
λ> negate . sum $ [1, 2, 3, 4, 5]
-15 
```

#### 无点样式

Pointfree 指的是一种不用指定参数就能组合函数的风格。“pointfree”中的“point”指的是参数，而不是(看起来)函数复合操作符。在某种意义上，我们添加“点”(操作符)是为了能够删除点(参数)。

```
λ> f = negate . sum
λ> :t f
f :: (Num c, Foldable t) => t c -> c
λ> f [1, 2, 3, 4, 5]
-15 
```

虽然`f`有一个参数，但我们没有指定它。

```
f :: Int -> [Int] -> Int

f z xs = foldr (+) z xs -- pointfull, all arguments specified

f = foldr (+) -- pointfree, arguments not specified 
```

```
λ> f 0 [1..5]
15 
```

构图的另一个例子:

```
putStrLn :: String -> IO ()
show :: Show a => a -> String

print :: Show a => a -> IO ()
print = putStrLn . show 
```

## [递归](https://dev.to/gillchristian/day-15-more-functional-patterns-and-recursion-4mic#recursion)

递归是通过自引用表达式根据函数本身来定义函数。这意味着函数将继续调用自身并重复其行为，直到满足某个条件返回结果。

来自于λ演算的[定点组合子](https://en.wikipedia.org/wiki/Fixed-point_combinator)(或 Y 组合子)。

经典的递归例子是阶乘(`!`)。

```
4! = 4 * 3 * 2 * 1
        12 * 2 * 1
            24 * 1
                24
4! = 24 
```

我们可以把它定义为一个递归函数。

```
brokenFactorial :: Integer -> Integer
brokenFactorial n = n * brokenFactorial (n - 1) 
```

但是这有一个问题，它一直无限地调用自己，或者实际上直到我们用完堆栈。

我们必须定义一个基本情况。

```
factorial :: Integer -> Integer
factorial 0 = 1
factorial n = n * factorial (n - 1) 
```

使我们的基本情况成为函数的相同值(在这种情况下是乘法)意味着将函数应用于这种情况不会改变先前应用的结果。

### 底部

或者 bottom 是 Haskell 中使用的一个术语，指的是没有成功产生值的计算。底层的两个主要变化是计算失败并有错误或者终止失败(循环)。

关于底部就不多说了。

**错误**

```
f :: Bool -> Int
f True = error "sarasa"
f False = 0 
```

```
λ> f False
0
λ> f True
*** Exception: sarasa
CallStack (from HasCallStack):
  error, called at <interactive>:1:10 in interactive:Ghci1 
```

**循环**

```
λ> x = x in x
*** Exception: <<loop>> 
```

## 结论

Haskell 不仅是一种函数式语言，它还提供了一些很好的东西，让编写代码变得非常有表现力。我个人是模式匹配、被定义为中缀操作符的函数和[多态性模型](https://dev.to/gillchristian/day-13-types-59m6#polymorphism)的粉丝。每当我用不支持这些特性的其他语言编写代码时，我真的很怀念这些特性。

同时，这也是这本书开始变得有趣的时候。从现在开始，我们将会看到更多的代码🎉