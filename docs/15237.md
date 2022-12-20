# 重新学习 Haskell(第 2 部分:列表理解、元组和类型)

> 原文：<https://dev.to/awwsmm/relearn-you-a-haskell-part-2-list-comprehensions-tuples-and-types-g29>

这是我关于 Haskell 的快速博客系列文章的继续。对于那些不久前可能学过一点 Haskell，但没怎么用过，并且已经忘记了大部分所学内容的人来说，这意味着是对 Haskell 语法和特性的复习。这基本上是对 [Learn You a Haskell](http://learnyouahaskell.com) 的一个快速总结，这是一本超级棒的书，你绝对应该花钱去买。

(注意:除了 LYAH 之外，还有更多可用的资源。例如，看看哈斯克尔的三位设计师在 1999 年写的这篇论文。)

# 列举理解

列表理解有一个*输出函数*，一个或多个*输入集*，以及一个或多个*谓词*，按此顺序排列。一个基本的列表理解看起来像:

```
ghci> [ <output function> | <input set>, ..., <predicate>, ... ] 
```

Enter fullscreen mode Exit fullscreen mode

输入集是按顺序输入到输出函数的值列表。最终，生成的(输出)列表将包含输入集的所有值，这些值一旦通过输出函数，就满足谓词。例如:

```
ghci> [ x*x | x <- [1..10], mod x 2 == 0 ]
[4,16,36,64,100] 
```

Enter fullscreen mode Exit fullscreen mode

上面打印了所有值`x`的平方，其中`x`取自集合`[1..10]`，假设`mod x 2`等于`0`。另一种方式是，我们首先获取所有数字的列表`[1..10]`，并通过谓词对它们进行过滤(`mod x 2 == 0`意味着我们只获取偶数`2, 4, 6, 8, 10`)，然后对这些数字求平方(因此我们最终得到了`4, 16, 36, 64, 100`)。

具有多个输入集的列表理解将在每个可能的对(或三元组，或四元组，...)和一个包含多个谓词的理解将只返回满足所有谓词*的值。例如:* 

```
ghci> [ (x,y) | x <- [1..3], y <- [4..6] ]
[(1,4),(1,5),(1,6),(2,4),(2,5),(2,6),(3,4),(3,5),(3,6)] 
```

Enter fullscreen mode Exit fullscreen mode

```
ghci> take 10 [ x | x <- [1..], x > 10, x /= 21, odd x ]
[11,13,15,17,19,23,25,27,29,31] 
```

Enter fullscreen mode Exit fullscreen mode

以下是一些有趣但简单的理解:

### FizzBuzz

一个经典的编程面试问题。

```
ghci> [ if (x `mod` 15 == 0) then "FizzBuzz" else if (x `mod` 3 == 0) then "Fizz" else if (x `mod` 5 == 0) then "Buzz" else show x | x <- [1..100] ]
["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz","16","17","Fizz","19","Buzz","Fizz","22","23","Fizz","Buzz","26","Fizz","28","29","FizzBuzz","31","32","Fizz","34","Buzz","Fizz","37","38","Fizz","Buzz","41",... 
```

Enter fullscreen mode Exit fullscreen mode

### 筛厄拉多塞

这种列表理解产生素数。

```
ghci> take 10 [ round x | x <- [2..], let l = [2..(ceiling(sqrt(x)))], all (/=0) (map (mod (round x)) l) ]
[3,5,7,11,13,17,19,23,29,31] 
```

Enter fullscreen mode Exit fullscreen mode

### 斐波那契数列

使用黄金分割比率生成斐波纳契数列。

```
ghci> let phi = ((1.0 + sqrt 5.0) / 2.0) in take 20 [ round (phi**x / (sqrt 5.0)) | x <- [1..] ]
[1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987,1597,2584,4181,6765] 
```

Enter fullscreen mode Exit fullscreen mode

...记住 Haskell 中的字符串只是字符列表，所以你也可以用它们来执行列表理解。这个列表列出了句子中所有的元音，按照它们在句子中出现的顺序:

```
ghci> [ x | x <- "The quick brown fox jumps over the lazy dog.", x `elem` "aeiouy" ]
"euioouoeeayo" 
```

Enter fullscreen mode Exit fullscreen mode

# 元组

在 Haskell 中，列表是同构的——它们只能存储一种值(`Num`、`Bool`、`Char`等)。).如果要存储异构值，需要使用一个元组(用括号创建):

```
ghci> [ True, 4, 'd']

<interactive>:18:12: error:
    • Couldn't match expected type ‘Bool’ with actual type ‘Char’
    • In the expression: 'd'
      In the expression: [True, 4, 'd']
      In an equation for ‘it’: it = [True, 4, 'd']

ghci> (True, 4, 'd')
(True,4,'d') 
```

Enter fullscreen mode Exit fullscreen mode

Haskell 不会区分不同长度的列表的类型，只要它们包含相同类型的数据。因此，`Num`的列表可以有多种长度的子列表:

```
ghci> [[], [0], [0,1], [0,1,2]]
[[],[0],[0,1],[0,1,2]] 
```

Enter fullscreen mode Exit fullscreen mode

这与元组的情况不同，元组对不同于三元组，三元组不同于四元组...即使它们包含相同种类的数据:

```
ghci> [(0,1), (0,1,2)]

<interactive>:22:9: error:
    • Couldn't match expected type ‘(a, b)’
                  with actual type ‘(Integer, Integer, Integer)’
    • In the expression: (0, 1, 2)
      In the expression: [(0, 1), (0, 1, 2)]
      In an equation for ‘it’: it = [(0, 1), (0, 1, 2)]
    • Relevant bindings include
        it :: [(a, b)] (bound at <interactive>:22:1) 
```

Enter fullscreen mode Exit fullscreen mode

用`fst`得到一对(一个二元组)的第一个元素，用`snd` :
得到第二个元素

```
ghci> fst ('a', 2)
'a'

ghci> snd ('a', 2)
2 
```

Enter fullscreen mode Exit fullscreen mode

用`zip`将两个列表逐个元素压缩成对。注意，较长的列表(包括无限列表)总是被截断成较短列表的长度:

```
ghci> zip [1..] "hello"
[(1,'h'),(2,'e'),(3,'l'),(4,'l'),(5,'o')] 
```

Enter fullscreen mode Exit fullscreen mode

# 类型和类别

您可以使用`:t`命令:
来检查`ghci`中的对象或方法的类型

```
ghci> :t (3<5) -- evaluates to True, a Bool
(3<5) :: Bool

ghci> :t "hello" -- a list of Chars ([Char]) is a String
"hello" :: [Char]

ghci> :t max -- a function which takes two Ord-erable objects of a type 'a' and returns a third
max :: Ord a => a -> a -> a 
```

Enter fullscreen mode Exit fullscreen mode

在这里，`a`是一个泛型类型，就像 Java 中的`T`。类型签名在`=>`之前的位是一个约束，在这种情况下，它表示类型`a`必须是`Ord`的后代(相当于 Java 中的`a extends Ord`)。如果您声明一个没有显式类型签名的函数，您可以使用`:t` :
来探索它的推断类型签名

```
ghci> length' xs = sum [ 1 | _ <- xs ]

ghci> length' [1,2,3,4,5]
5

ghci> :t length'
length' :: Num a => [t] -> a 
```

Enter fullscreen mode Exit fullscreen mode

上面，我们看到我的`length'`方法接受`[t]`(一个类型为`t`的对象列表)并返回`a`，它必须是从`Num` -ber 类派生的一个对象。您可以并且应该显式声明函数类型签名:

```
ghci> let inc :: Integer -> Integer; inc x = x + 1

ghci> inc 3
4

ghci> :t inc
inc :: Integer -> Integer 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我定义了方法`inc`，它接受一个`Integer`并返回一个加 1 的`Integer`。

注意`+`、`-`、`==`、`/=`等等也是函数，默认情况下只是中缀函数。要将它们传递给`:t`(或任何其他函数)，请用括号将它们括起来:

```
ghci> :t (+)
(+) :: Num a => a -> a -> a

ghci> :t (/=)
(/=) :: Eq a => a -> a -> Bool 
```

Enter fullscreen mode Exit fullscreen mode

## 内置类型

Haskell [有一些预定义的类型](http://www2.informatik.uni-freiburg.de/~thiemann/haskell/haskell98-report-html/basic.html)，我已经提到过其中的一些:

```
ghci> :t False -- False and True are Bool-ean types
False :: Bool

ghci> :t "hi" -- lists of Chars are synonymous with Strings
"hi" :: [Char]

ghci> x :: String; x = "hi" -- explicit type declaration

ghci> :t x
x :: String

ghci> :t 'h' -- note: Char, not [Char]
'h' :: Char

ghci> :t [1,2] -- lists are homogeneous
[1,2] :: Num a => [a]

ghci> :t ['a','b'] -- so they have type constraints
['a','b'] :: [Char]

ghci> :t [1.1,2.2]
[1.1,2.2] :: Fractional a => [a]

ghci> :t (1,'a') -- tuples have per-member type constraints
(1,'a') :: Num a => (a, Char)

ghci> :t () -- the empty tuple is a special type, the unit datatype, ()
() :: ()

ghci> :t Nothing -- Nothing and Just are of type Maybe
Nothing :: Maybe a

ghci> :t Left -- Left and Right are of type Either
Left :: a -> Either a b

ghci> :t LT -- LT, EQ, and GT are of type Ordering
LT :: Ordering 
```

Enter fullscreen mode Exit fullscreen mode

...诸如此类。Haskell 中还存在`Double`、`Float`、`Int`、`Integer`和其他预定义的类型，但是——因为类型推理给了变量最大可能的范围(通常是数字的`Num`或`Fractional`)——您必须显式地将变量声明为这些较窄的类型之一:

```
ghci> x :: Double; x = 3.14

ghci> :t x
x :: Double

ghci> x :: Float; x = 3.14

ghci> :t x
x :: Float

ghci> x :: Integer; x = 42

ghci> :t x
x :: Integer

ghci> x :: Int; x = 42

ghci> :t x
x :: Int 
```

Enter fullscreen mode Exit fullscreen mode

然而，`Int`和`Integer`有什么区别呢？`Int`有界(且快)，但`Integer`不有界(且慢):

```
ghci> factorial :: Int -> Int; factorial n = product [1..n]  

ghci> factorial 10 -- okay, this is fine
3628800

ghci> factorial 100 -- (silent) overflow!
0

ghci> factorial :: Integer -> Integer; factorial n = product [1..n]

ghci> factorial 100 -- woo! works great!
93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000

ghci> factorial 10000 -- also works fine! so many digits...
2846259680917054518906413212119868890148051401702799230794179994274411340003764443772990786757784775815884062142317528830042339940153518739052421161382716174819824199827592418289259787898124253120594659962598670656016157203603239792632873671705574197596209947972034615369811989709261127750048419884541047554464244213657330307670362882580354896746111709736957860367019107151273058728104115864056128116538532596842582599558468814643042558983664931705925171720427659740744613340005419405246230343686915405940406622782824837151203832217864462718382292389963899282722187970245938769380309462733229257055545969002787528224254434802112755901916942542902891690721909708369053987374745248337289952180236328274121704026808676921045155584056717255537201585213282903427998981844931361064038148930449962159999935967089298019033699848440466541923625842494716317896119204123310826865107135451684554093603300960721034694437798234943078062606942230268188522759205702923084312618849760656074258627944882715595683153344053442544664841689458042570946167361318760523498228632645292152942347987060334429073715868849917893258069148316885425195600617237263632397442078692464295601230628872012265295296409150830133663098273380635397290150658182257429547589439976511386554120812578868370423920876448476156900126488927159070630640966162803878404448519164379080718611237062213341541506599184387596102392671327654698616365770662643863802984805195276953619525924093090861447190739076858575593478698172073437209310482547562856777769408156407496227525499338411280928963751699021987049240561753178634693979802461973707904186832993101655415074230839317... 
```

Enter fullscreen mode Exit fullscreen mode

对我来说(主要是有 C/C++/Java 背景的人),这相当不错。

## 内置类

Haskell 类(也称为 typeclasses)有点像 Java 接口，因为从特定父类派生的任何子类都保证实现一些特定的行为。

一些比较常见的包括:

### [`Eq`](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Eq) 和 [`Ord`](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Ord)

实现`Eq`的类可以被测试是否相等。所有预定义的类(除了那些与 I/O 相关的)都实现了`Eq`。

类似地，实现了`Ord`的类可以使用`<`、`>`等等进行排序。所有的数字类型，以及`Char`和列表，都扩展了`Ord`类。

### [`Show`T4】](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Show)

实现`Show`的类可以表示为`String` s，任何`Show`实现类型的变量都可以通过`show`方法
转换为`String`

```
ghci> show True
"True"

ghci> show 1.1
"1.1" 
```

Enter fullscreen mode Exit fullscreen mode

### [`Read`T4】](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Read)

`Read`可以认为是`Show`的反义词。`Read`类将`String`解析为适当类型的变量，其中“适当类型”由变量是`read` :
的方式决定

```
ghci> read "4.8" + 2.0
6.8

ghci> read "[1,2,3]" ++ [4,5,6]
[1,2,3,4,5,6] 
```

Enter fullscreen mode Exit fullscreen mode

`read`-对一个变量做任何事情都会抛出一个错误，因为 Haskell 不知道给它什么类型:

```
ghci> read "4.8"
*** Exception: Prelude.read: no parse 
```

Enter fullscreen mode Exit fullscreen mode

你可以用一个显式的类型注释来解决这个问题:

```
ghci> read "4.8" :: Double
4.8 
```

Enter fullscreen mode Exit fullscreen mode

### [`Bounded`T4】](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Bounded)

`Bounded`类型有最大和最小限制。你可以用`minBound`和`maxBound`看看这些是什么:

```
ghci> minBound :: Int
-9223372036854775808

ghci> maxBound :: Int
9223372036854775807

ghci> minBound ::Bool
False

ghci> maxBound :: Bool
True 
```

Enter fullscreen mode Exit fullscreen mode

### [`Num`T4】](http://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#t:Num)

`Num`是 Haskell 中的基本数值类。任何扩展了`Num`的类都必须实现`+`、`*`、`abs`、`signum`、否定以及其他一些东西。`Real`和`Fractional`都来源于`Num`。(而`Real`也来自`Ord`。)

`Fractional`由预定义的非整数数字类`Float`和`Double`实现，而`Int`和`Integer`实现了`Integral`类，该类本身实现了`Real`类。

概述所有这些[的类层次结构可以在 Haskell 98 报告](https://www.haskell.org/onlinereport/basic.html)中找到。

* * *

和往常一样， [Learn You a Haskell](http://learnyouahaskell.com/types-and-typeclasses#believe-the-type) 对类型和类做了很好的解释，并且比我在这里做的更详细。我强烈推荐。

我希望这篇文章能唤起你一些关于在 Haskell 中使用列表理解、元组和类型的记忆。至此，您应该已经了解了足够多的知识，可以出去完成一些编码挑战了！对于一行程序来说，这是一种很棒的语言！

*接下来是第 3 部分:功能*