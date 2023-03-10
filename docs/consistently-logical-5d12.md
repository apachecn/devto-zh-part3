# 一贯符合逻辑

> 原文：<https://dev.to/involans/consistently-logical-5d12>

在最近的 [538 谜语](https://fivethirtyeight.com/features/dont-trust-anyone-older-than-l/)中，发布了一个特殊的逻辑谜题，涉及到确定谁在说真话，如果你假设他们的说法都必须一致，并且他们满足一个额外的约束，即所有说谎者都比所有说真话者年龄大。我将展示如何用代码解决这个问题，特别是使用 Haskell 函数式编程。

# 建模问题

解决任何问题的第一步是决定如何在拼图中表示信息。通常，对这些输入的操作是从我们定义的结构中自然产生的。

这个问题采取一组关于谜题中其他参与者年龄的声明的形式，其中每个声明都声明某人的年龄*是*的值，*不是*的值，*大于*的值，或者*小于*的值。因此，让我们将声明表示为操作和值的元组:

```
type Claim = (Op, Int) 
```

我们需要决定`Op`是什么。最直接的方法是 sum-type，它为我们需要的四个操作中的每一个都提供了一个构造函数，所以:

```
data Op = Is | Isnt | Gt | Lt deriving (Show, Eq) 
```

然而，当索赔者撒谎时，我们并没有获得*或*信息，相反，我们可以推断出运算符的逆运算——例如，如果有人撒谎说 *X 是 17* ，那么这就相当于有人说了实话，说 *X 不是 17* 。对于`>`和`<`来说也是如此，但是需要注意的是，这些需要`>=`和`<=`操作符来传递逆运算。

```
data Op = Is | Isnt | Gt | Gte | Lt | Lte deriving (Show, Eq, Ord)

invert :: Op -> Op
invert Is   = Isnt
invert Isnt = Is
invert Gt   = Lte
invert Gte  = Lt
invert Lt   = Gte
invert Lte  = Gt 
```

然后我们需要为索赔者本身建模。出于我们的目的，每个岛民都由一个字母标识，因此我们将使用`Char` s 来表示我们的岛民姓名:

```
type Islander = Char 
```

索赔人是岛民，他们的索赔集也是岛民。

```
data Claimant = Claimant
  { claimantName   :: Islander
  , claimantClaims :: Set (Islander, Claim)
  } deriving (Show, Eq) 
```

例如，如果“X 说 Y 超过 10 岁”，我们可以将此编码为:

```
Claimant 'X' (S.fromList [('Y', (Gt, 10))]) 
```

这就是我们对 538 描述中指定的问题进行编码所需要的全部内容，有了几个助手，我们可以这样做，它看起来与问题陈述本身非常相似。

```
-- the islanders in the problem, with their claims
islanders :: [Claimant]
islanders = ['a' `saysThat` ['b' ==> (Gt, 20)
                            ,'d' ==> (Gt, 16)
                            ]
            ,'b' `saysThat` ['c' ==> (Gt, 18)
                            ,'e' ==> (Lt, 20)
                            ]
            ,'c' `saysThat` ['d' ==> (Lt, 22)
                            ,'a' ==> (Is, 19)
                            ]
            ,'d' `saysThat` ['e' ==> (Isnt, 17)
                            ,'b' ==> (Is, 20)
                            ]
            ,'e' `saysThat` ['a' ==> (Gt, 21)
                            ,'c' ==> (Lt, 18)
                            ]
            ]
  where saysThat who cs = Claimant who (S.fromList cs)
        (==>) = (,) 
```

# 你在暗示什么？

一旦我们知道每个岛民声称什么是真实的，我们如何才能发现他们的说法是否相互一致。例如，我们可以看到‘A’表示‘B 大于 20’，但是‘D’表示‘B 是 20’。很明显，他们不可能都是对的，但是我们如何通过算法来确定呢？

好吧，我们可以看看每个声明意味着什么。每个声明相当于说一个值位于一个或多个值的范围内。例如,`Gt 10`等同于说“在范围 11 内”，并且`Is 12`与范围 12 中的‘相同..12'.我们可以将`Isnt`建模为一个值必须位于两个范围之一，这两个范围都不包括该值，因此`Isnt 12`意味着“必须位于该范围内”..11，或在 13 的范围内。

这让我们对每个声明的含义有了一个概念，以及我们如何表示它们:

```
data InclusiveRange = Maybe Int :..: Maybe Int
  deriving (Show, Eq)

newtype Implication = OneOf { ranges :: NonEmpty InclusiveRange }
  -- deriving Semigroup makes 'or'-ing ranges easy
  deriving (Show, Eq, Semigroup) 
```

然后，我们可以继续为我们可以表达的各种声明的不同含义编写智能构造函数:

```
-- helper that makes these functions tidier
inRange :: InclusiveRange -> Implication
inRange = OneOf . pure

is :: Int -> Implication
is x = inRange $ pure x :..: pure x

isnt :: Int -> Implication
isnt x = lt x <> gt x

gt :: Int -> Implication
gt x = inRange $ pure (x + 1) :..: Nothing

gte :: Int -> Implication
gte x = inRange $ pure x :..: Nothing

lt :: Int -> Implication
lt x = inRange $ Nothing :..: pure (x - 1)

lte :: Int -> Implication
lte x = inRange $ Nothing :..: pure x 
```

我们可以将这些结合在一个函数中，从一个声明到它的含义:

```
implication :: Claim -> Implication
implication (op, age) = ($ age) $ case op of
  Is   -> is
  Isnt -> isnt
  Gt   -> gt
  Gte  -> gte
  Lt   -> lt
  Lte  -> lte 
```

# 做出推论

下一步是看我们如何把两个含义结合起来得到逻辑结果。例如，从`Gt 10`和`Lt 15`我们应该能够推断出该值位于范围`11..14`内。这意味着某种同素结构，其中的含义可以组合起来，以创建更详细的。在这种情况下，我们需要`mempty`基础案例:

```
anything :: Implication
anything = inRange $ Nothing :..: Nothing 
```

作为第一步，我们将看看我们可以从两个含义中推断出什么:

```
-- apply a binary operation, taking lhs if rhs is Nothing, or rhs if
-- lhs is Nothing
safeBinOp :: (a -> a -> a) -> Maybe a -> Maybe a -> Maybe a
safeBinOp f ma mb = liftA2 f ma mb <|> ma <|> mb

infer :: Implication -> Implication -> Maybe Implication
infer (OneOf lhs) (OneOf rhs) = fmap OneOf . NE.nonEmpty . catMaybes
  $ liftA2 infer' (NE.toList lhs) (NE.toList rhs)
  where
    infer' (a :..: b) (a' :..: b') =
      let lb = safeBinOp max a a'
          ub = safeBinOp min b b'
       in if fromMaybe False (liftA2 (>) lb ub)
             then Nothing -- invalid, lb > ub
             else pure (lb :..: ub) 
```

从两个含意的推论是我们通过观察每个范围对另一边的每个范围应用什么约束而得到的。任何结果无效的范围都被`catMaybes`移除，如果我们根本没有得到任何范围，我们就不能返回任何蕴涵，这意味着这两个语句是矛盾的。

让我们定义一种方法来漂亮地打印一个蕴涵，然后用这个推理机制来旋转一下！

```
showImpl :: Implication -> String
showImpl = L.intercalate ";" . fmap showRange . NE.toList . ranges
  where
    showRange (Nothing :..: Just x) = ".." <> show x
    showRange (Just x :..: Nothing) = show x <> ".."
    showRange (Just x :..: Just y)  = if x == y
                                         then show x
                                         else show x <> ".." <> show y
    showRange _   = "anything"

-- Give a two-parameter kleisli arrow, apply it to two arguments
-- (hard to believe this isn't in Control.Monad tbh)
liftKleisli :: Monad m => (a -> b -> m c) -> m a -> m b -> m c
liftKleisli f a b = liftA2 (,) a b >>= uncurry f 
```

现在我们可以玩一下`infer`:

```
λ> let p = putStrLn . maybe "Contradiction" showImpl
λ> let inferAll = foldr (liftKleisli infer) (pure anything) . fmap pure
λ> p $ inferAll [anything]
anything
λ> p $ inferAll [gt 5, lt 10]
6..9
λ> p $ inferAll [gt 5, lt 10, isnt 7]
6;8..9
λ> p $ inferAll [gt 5, lt 10, isnt 7, isnt 8]
6;9
λ> p $ inferAll [gt 5, lt 10, isnt 7, isnt 8, isnt 6]
9
λ> p $ inferAll [gt 5, lt 12, isnt 8, isnt 10]
6..7;9;11
λ> p $ inferAll [gt 20, is 20]
Contradiction 
```

太好了，我们似乎有一个工作的推理机。我们现在可以用幺半群来概括:

```
newtype Consequence = Consequence { getConsequence :: Maybe Implication }
  deriving (Show, Eq)

instance Semigroup Consequence where
  (Consequence ma) <> (Consequence mb) = Consequence (liftKleisli infer ma mb)

instance Monoid Consequence where
  mempty = Consequence (pure anything)
  mappend = (<>)

consequence :: Implication -> Consequence
consequence = Consequence . pure 
```

# 寻找解决办法

我们现在有了很多我们需要找到解决方案的碎片，我们可以着手的一个方法是列举岛上所有可能的说谎者和讲真话者的排列，检查哪些是内在一致的，然后检查所有的说谎者都比讲真话者年长。在我们的例子中，这是非常容易处理的，因为我们只需要考虑 32 种安排。

首先，我们需要将一个岛民标记为说谎者或实话实说者，虽然我们可以为此使用`Bool`,但最好使用特定的数据类型:

```
data Honesty = Honest | Liar deriving (Eq, Show) 
```

那么我们需要这两个值的所有 32 种排列，例如:

*   诚实，诚实，诚实，诚实，诚实
*   诚实，诚实，诚实，诚实，骗子
*   诚实，诚实，诚实，骗子，诚实
*   等等...

让我们为此创建一个小助手:

```
-- produce an arrangement of xs of size n
arrangement :: [a] -> Int -> [[a]]
arrangement _  0 = [[]]
arrangement xs n = xs >>= \x -> (x:) <$> arrangement xs (n - 1) 
```

我们可以快速检查这是否符合我们的要求:

```
λ> length $ arrangement ['a', 'b'] 5
32
λ> take 3 $ arrangement ['a', 'b'] 5
["aaaaa","aaaab","aaaba"] 
```

现在，我们可以生成我们需要考虑的所有 32 个潜在解决方案:

```
fmap (`zip` islanders) $ arrangement [Honest, Liar] (length islanders) 
```

为了找到一个解决方案，我们需要将所有关于岛民的说法结合在一起，如果这些说法来自说谎者，就可能将它们颠倒过来，然后检查是否有任何矛盾，以及是否符合年龄规则。首先，让我们定义解决方案的类型及其结论:

```
type Conclusions = Map Char Consequence
type Solution = [(Honesty, Claimant)] 
```

我们需要一种方法来将一个解决方案转化为关于其所有主张的结论:

```
conclusions :: [(Honesty, S.Set (Char, Claim))] -> Conclusions
conclusions solution =
  M.fromListWith (<>)
  . fmap (fmap (consequence . implication))
  $ [(who, (trust h op, val)) | (h, cs) <- solution
                              , (who, (op, val)) <- S.toList cs
    ]
  where trust Liar = invert
        trust Honest = id 
```

以及知道一组结论是否包含任何矛盾的方法:

```
-- A set of conclusions is valid if there are no contradictions
viable :: Conclusions -> Bool
viable = all (isJust . getConsequence) 
```

以及一种确定它是否符合年龄限制规则的方法，在这种情况下，我们检查最老的诚实的人比最年轻的说谎者年轻——或者用范围的语言来说，诚实的申请者的年龄的最高下限低于说谎者的年龄的最低上限。

```
meetsAgeLimitRule :: Solution -> Conclusions -> Bool
meetsAgeLimitRule claimants conc =
  let oldestHonest = getAge max lowerBound isHonest
      youngestLiar = getAge min upperBound (not . isHonest)
   in fromMaybe True (liftA2 (<) oldestHonest youngestLiar)
 where
   honest = S.fromList [who | (Honest, Claimant who _) <- claimants]
   isHonest = (`S.member` honest)

   lowerBound (OneOf cs) = let f (lb :..: _) = lb in safely min (f <$> cs)
   upperBound (OneOf cs) = let f (_ :..: ub) = ub in safely max (f <$> cs)

   getAge overall perPerson isRelevant
       = safely overall
       . fmap (getConsequence . snd >=> perPerson)
       . filter (isRelevant . fst)
       $ M.toList conc

-- safely apply a bin-op over a foldable of Maybes
safely :: (Foldable f, Ord a) => (a -> a -> a) -> f (Maybe a) -> Maybe a
safely f = foldr (safeBinOp f) Nothing 
```

一切就绪后，我们可以继续搜索所有可能的安排，并打印出我们找到的可行安排:

```
viables :: [(Solution, Conclusions)]
viables = filter (uncurry meetsAgeLimitRule)
        . filter (viable . snd)
        . fmap   (withConclusion . (`zip` islanders))
        $ arrangement [Honest, Liar] (length islanders)

withConclusion :: Solution -> (Solution, Conclusions)
withConclusion cs = (cs, conclusions (fmap claimantClaims <$> cs))

-- print out all the viable solutions
main :: IO ()
main = mapM_ printSolution (zip [1 ..] viables)
  where
    printSolution (n, (sol, conc)) = do
      putStrLn $ "Solution: " <> show n
      putStrLn $ replicate 15 '-'
      mapM_ (go conc) sol
      putStrLn ""
    go conc (h, c) = putStrLn
                   $ unwords [[claimantName c]
                             ,show h
                             ,maybe "" showImpl
                              (M.lookup (claimantName c) conc >>= getConsequence)
                             ] 
```

# TaDa!

只有一个解决办法:

```
Solution: 1
---------------
a Liar 19
b Liar 20
c Honest 18
d Honest ..16
e Liar 20.. 
```

从结论中我们可以清楚地看到，这是内在一致的。用这个解决方案手动完成这个难题应该足以让你相信这个解决方案是合理的，但在我看来，最好的部分是它是如何自然地从观察各部分的构造方式，从看到范围的概念正好落在声明之外，到我们可以使用它们的幺正结构将声明折叠在一起，然后能够从我们计算的结果中读出结论。能够清楚地定义解决方案使得解决方案的实施变得更加容易。