# 在 PureScript 中测试银行形

> 原文：<https://dev.to/riccardoodone/testing-bank-kata-in-purescript-16k8>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-03-18-testing-bank-kata-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

[![](img/2877118000e2d13529f9e5283ff1457a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s91P95fm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACQ1bneAfNmhYuocE6f8gxg.jpeg)

## [T1】简介](#intro)

上周，我们在 PureScript 中解出了[银行形。现在是时候添加一些单元测试了。](https://dev.to/riccardoodone/bank-kata-in-purescript-3dmp)

特别是，我们将测试形的三个主要功能:

```
deposit :: Int -> StateT (Array Transaction) Effect Unit

withdraw :: Int -> StateT (Array Transaction) Effect Unit

printStatement :: StateT (Array Transaction) Effect Unit 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

先说`deposit` :

```
deposit :: Int -> StateT (Array Transaction) Effect Unit
deposit amount = do
ts <- lift nowDateTime
let t = Deposit { timestamp: ts, amount: amount }
modify\_ \ts -> ts <> [t] 
```

Enter fullscreen mode Exit fullscreen mode

可惜它用的是`Effect`。这意味着，它做了一些不纯的事情，我们无法在单元测试中检查。

我们可以通过将类型签名改为
来轻松解决这个问题

```
deposit
 :: forall m. Monad m
 => Int
 -> StateT (Array Transaction) m Unit 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，我们不再指定具体的单子(`Effect`)。我们只是说`deposit`使用一个单子`m`作为`StateT`的基础单子。

可悲的是，这并不编译。事实上，类型签名是在说谎。在函数体中我们做`ts <- lift nowDateTime`。正如在[上一篇文章](https://dev.to/riccardoodone/bank-kata-in-purescript-3dmp)中所解释的，这使得函数必须使用`Effect`。

幸运的是，这很容易解决。在`deposit`中不使用`nowDateTime`，我们将直接注入:

```
deposit
 :: forall m. Monad m
 => m DateTime
 -> Int
 -> StateT (Array Transaction) m Unit 
```

Enter fullscreen mode Exit fullscreen mode

这种重构的缺点是，我们需要将生产代码从`deposit 500`更改为`deposit nowDateTime 500`。好处是我们现在可以使用一个单元可测试单子。没那么糟！

下面是测试

```
testDeposit :: Effect Unit
testDeposit = do
 ts <- nowDateTime
 let amount = 1
 expected = Identity [Deposit {amount: amount, timestamp: ts}]
 actual = execStateT (deposit (Identity timestamp) amount) []
 assertEqual { actual: actual, expected: expected } 
```

Enter fullscreen mode Exit fullscreen mode

我们将`timestamp :: DateTime`包装在`Identity`单子中，这样`deposit (Identity timestamp) amount`就有了类型`StateT (Array Transaction) Identity Unit`。这意味着，`execStateT`返回`Identity (Array Transaction)`。

测试`withdraw`遵循完全相同的模式，所以我们不打算覆盖它。

让我们转到`printStatement` :

```
printStatement :: StateT (Array Transaction) Effect Unit
printStatement = do
 s <- gets toStatement
 lift $ log s 
```

Enter fullscreen mode Exit fullscreen mode

这里的故事与我们对`deposit` :
所做的非常相似

```
printStatement :: forall m. Monad m => (String -> m Unit) -> StateT (Array Transaction) m Unit
printStatement logger = do
 s <- gets toStatement
 lift $ logger s 
```

Enter fullscreen mode Exit fullscreen mode

以及相应的单元测试:

```
testPrintStatementWithTransactions :: Effect Unit
testPrintStatementWithTransactions = do
 timestamp <- nowDateTime
 let d = Deposit { amount: 500, timestamp: timestamp }
 w = Withdraw { amount: 100, timestamp: timestamp }
 state = [d, w]
 expected = "expected string"
 actual = execWriter (execStateT (printStatement \s -> tell s) state)
 assertEqual { actual: actual, expected: expected } 
```

Enter fullscreen mode Exit fullscreen mode

注意，作为基本单子，我们使用 [`Writer`](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.Writer) 。这个单子给了我们对`tell`的访问，这允许我们添加到一个累加器中。这样`printStatement`在累加器而不是控制台中“写”语句。

## 显示代码

代码:

```
data Transaction
  = Deposit Info
  | Withdraw Info

derive instance eqTransaction :: Eq Transaction

instance showTransaction :: Show Transaction where
  show (Deposit i) = show i
  show (Withdraw i) = show i

type Info =
  { timestamp :: DateTime
   , amount    :: Int
  }

deposit :: forall m. Monad m => m DateTime -> Int -> StateT (Array Transaction) m Unit
deposit nowDateTime amount = do
  ts <- lift nowDateTime
  let t = Deposit { timestamp: ts, amount: amount }
  modify_ \ts -> ts <> [t]

withdraw :: forall m. Monad m => m DateTime -> Int -> StateT (Array Transaction) m Unit
withdraw nowDateTime amount = do
  ts <- lift nowDateTime
  let t = Withdraw { timestamp: ts, amount: amount }
  modify_ \ts -> ts <> [t]

printStatement :: forall m. Monad m => (String -> m Unit) -> StateT (Array Transaction) m Unit
printStatement logger = do
  s <- gets toStatement
  lift $ logger s

toStatement :: Array Transaction -> String
toStatement =
  fst <<< foldl fnc (Tuple "" 0)
  where
  fnc (Tuple s i) (Deposit d) =
    Tuple (s <> "\n" <> joinWith " " [ show d.timestamp, show d.amount, show $ i + d.amount]) (i + d.amount)
  fnc (Tuple s i) (Withdraw w) =
    Tuple (s <> "\n" <> joinWith " " [ show w.timestamp, "-" <> show w.amount, show $ i - w.amount]) (i - w.amount)

main :: Effect Unit
main = do
  flip evalStateT [] do
    deposit nowDateTime 500
    withdraw nowDateTime 100
    printStatement log 
```

Enter fullscreen mode Exit fullscreen mode

测试:

```
main :: Effect Unit
main = do
  testDeposit
  testWithdraw
  testPrintStatementNoTransactions
  testPrintStatementWithTransactions

testDeposit :: Effect Unit
testDeposit = do
  timestamp <- nowDateTime
  let amount = 1
      expected = Identity [ Deposit { amount: amount, timestamp: timestamp } ]
      actual = execStateT (deposit (Identity timestamp) amount) [] 
  assertEqual { actual: actual, expected: expected }

testWithdraw :: Effect Unit
testWithdraw = do
  timestamp <- nowDateTime
  let amount = 1
      expected = Identity [ Withdraw { amount: amount, timestamp: timestamp } ]
      actual = execStateT (withdraw (Identity timestamp) amount) [] 
  assertEqual { actual: actual, expected: expected }

testPrintStatementNoTransactions :: Effect Unit
testPrintStatementNoTransactions = do
  let expected = ""
      actual = execWriter (evalStateT (printStatement \s -> tell s) [])
  assertEqual { actual: actual, expected: expected }

testPrintStatementWithTransactions :: Effect Unit
testPrintStatementWithTransactions = do
  timestamp <- nowDateTime
  let d = Deposit { amount: 500, timestamp: timestamp }
      w = Withdraw { amount: 100, timestamp: timestamp }
      state = [ d, w ]
      expected = "expected string"
      actual = execWriter (evalStateT (printStatement \s -> tell s) state)
  assertEqual { actual: actual, expected: expected } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！