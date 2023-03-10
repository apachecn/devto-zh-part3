# 滥用幺半群:幺半群谓词组合子

> 原文：<https://dev.to/involans/a-little-boolean-dsl-monoidal-predicate-combinators-2ebe>

Haskell 编写函数的简洁方式的一个好处是我们的谓词读起来非常自然:

```
filter (> 5) [0 .. 10] 
```

我们可以用正常的函数组合来组合一个产生`Bool`的函数链:

```
filter ((== 'c') . fst) [('a', 1), ('b', 2), ('c', 3)] 
```

但是通常你想用*和*、*或*和*而不是*来逻辑地组合谓词，尽管语法很简单，但它仍然会变得很吵，妨碍我们理解意思。想象一下决定顾客的决定过程:

```
newtype Age = Age Int deriving (Show, Eq, Ord)
newtype BloodAlcohol = MgMl Double deriving (Show, Eq, Ord)
data Person = Person { age :: Age, bloodAlcohol :: BloodAlcohol } 
```

可以在酒吧里喝一杯

```
let drinkingAge = Age 18
    legalLimit  = MgMl 80.0
    isOldEnough = (drinkingAge <=) . age
    isDrunk     = (legalLimit  <=) . bloodAlcohol
 in filter (\patron -> isOldEnough patron && not (isDrunk patron))
           [ Person (Age 17) (MgMl 0.0)  -- too young
           , Person (Age 22) (MgMl 93.5) -- too drunk
           , Person (Age 20) (MgMl 62.1) -- fine (for now)
           ] 
```

这很好，但是让线程将`patron`参数传递给两个谓词并组合结果，嗯，这感觉有点低级！抽象在哪里？这能不能更有宣示性？也许我们理想的语法应该是:

```
filter (isOldEnough <&> not' isDrunk) customers 
```

好多了！说真的，像这样表达意图意味着逻辑更清晰，更少被论证传递的仪式所掩盖，并且有希望更容易正确。

我记得我在 Hoogle 中搜索一个函数`f :: (b -> c -> d) -> (a -> b) -> (a -> c) -> a -> c`，这样我就可以将`(<&>)`创建为`f (&&)`，然后才意识到这可以通过几种不同的方式来实现。最明显的一个(也是在实际代码中一直使用的一个)是使用*函子*和*应用程序*(参见下面的简短讨论)，这是很自然的，因为我们在这里尝试做的是‘FM appy’类的事情。虽然 Hoogle 仍然没有给出该查询的最佳答案(虽然我们确实得到了`Data.Function.Tools.apply2Way`，它正好是我们需要的类型)，但是有几种方法可以解决这个问题，并且可能令人惊讶的是，*幺半群*是一个非常合适的(如果稍微有点迂回)方法。这是令人惊讶的，因为*应用*和*幺半群*有不同的种类，`* ->
*`对`*`，然而我们可以使用任何一种抽象来编码这个小布尔代数。

为此，我们需要提醒自己，幺半群让我们结合两个相似的东西。这样的例子有连接两个字符串，联合两个集合，添加两个数字。幺半群类有以下方法:

```
class Monoid m where
  mappend :: m -> m -> m -- also called (<>), and technically belongs to Semigroup
  mempty :: m            -- gets us the empty element 
```

我们正在尝试将两个相似的东西结合起来！具体来说，两个谓词通过`(&&)`或`(||)`组合成第三个谓词。我们可以通过命名这个想法来明确这一点:

```
type Pred a = a -> Bool 
```

我们能把我们的`Pred`当成幺半群吗？是啊！关键是，除了显而易见的东西，如字符串、集合和数字，函数也形成了幺半群，只要它们产生幺半群(这里写成 lambdas 是为了清楚地表明我们接受并返回函数):

```
instance Monoid b => Monoid (a -> b) where
  mempty      = \a -> mempty
  mappend f g = \a -> f a <> g a 
```

`mappend`实例特别有趣:它使我们能够组合两个函数产生第三个函数，但不是像`(.)`那样串行，而是并行。

不幸的是，我们的`Pred a`还不是幺半群，因为`Bool`没有(一个)幺半群实例。事实上有两个。谜题的第二部分是，因为某些数据类型有多个幺半群实例，它们的不同实例与新类型相关联，以允许我们指定我们指的是哪一个。

数字有`Sum`和`Product`，分别代表幺半群`(+, 0)`和`(*, 1)`。具有排序的对象( *Ord* 的成员)具有半群实例`Min`和`Max`(其中`(<>)`是`min`和`max`)以及*幺半群实例(如果它们是*有界的*)。布尔有`All`和`Any`，分别代表幺半群`(&&, True)`和`(||, False)`。考虑到这一点，我们可以创建我们的小谓词组合子:* 

```
combine :: (Functor f, Monoid (f m)) => (a -> m) -> (m -> b) -> f a -> f a -> f b
combine into outof f g = fmap outof (fmap into f <> fmap into g) 
```

通过允许选择幺半群的同构函数被传入，`comb`助手只是封装了 mappending two `Pred a`的公共逻辑。有了这个我们就得到:

```
(<&>), (<?>) :: Pred a -> Pred a -> Pred a
(<&>) = combine All getAll
(<?>) = combine Any getAny

not' :: Pred a -> Pred a
not' = (not .) -- helps us avoid brackets 
```

现在我们可以用它们自己的术语组合复杂的条件，产生一个简单的布尔 DSL，甚至能够处理复杂的嵌套条件:

```
canDrink :: Pred Person
canDrink = isTheBoss `<?>` (hasValidId `<&>` isOldEnough `<&>` not' isDrunk) 
```

虽然更高级别的*函子*和*应用程序*更适合函数，但看到同一件事可以从不同的方向处理总是令人满意的。

# [postscript 语言:](#postscript)

(更明显的)适用解决方案:

像幺半群一样，提醒我们自己`(->)`的适用和函子实例，函数的类型，这里为了清楚起见加上了特殊的签名，并且提醒我们自己`(->)`是用中缀符号写的，所以`(->) a b`和`a -> b`是等价的:

```
instance Functor ((->) a) where
  fmap :: (b -> c) -> (a -> b) -> (a -> c)
  fmap f g = f . g

instance Applicative ((->) a) where
  pure :: b -> (a -> b)
  pure = const

  (<*>) :: (a -> b -> c) -> (a -> b) -> (a -> c)
  f <*> g = \x -> f x (g x) 
```

虽然我们还没有在这里看到我们的`andP`，但是值得记住的是`pure (&&) <*> isOldEnough`的类型应该是:

```
(Person -> (Bool -> Bool -> Bool)) -> (Person -> Bool) -> Person -> Bool -> Bool 
```

如果我们有一个`Person -> Bool -> Bool`，我们可以在第二个谓词
上再次使用`<*>`

```
let appAnd = pure (&&)
    isOlderAnd = appAnd <*> isOldEnough
 in isOlderAnd <*> (not . isDrunk) 
```

或者，更简单地说(利用`<$>`，`fmap`的中缀同义词:

```
(&&) <$> isOldEnough <*> (not . isDrunk) 
```

当所有的中缀操作符开始让你的眼睛出血时，我们可以使用*应用型*类中的`liftA2`，它的作用就是:

```
liftA2 (&&) isOldEnough (not . isDrunk) 
```

这意味着(如果我们考虑到这是多么微不足道的话)，我们可以定义`(<&>) = liftA2 (&&)`