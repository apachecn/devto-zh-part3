# 纯脚本银行形

> 原文：<https://dev.to/riccardoodone/bank-kata-in-purescript-3dmp>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-03-13-bank-kata-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

[![](img/0136869e66db77c7f67250c9edde36d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MLZLt5r6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Awy9M28S1h8KHOav3b2DBBw.jpeg)

## [T1】简介](#intro)

我在 FP 方面很烂，我非常需要你的一些反馈。所以请不要对代码生气。如果你有任何反馈，请分享！！

## 武士形

让我们首先通过复制/粘贴牛逼的[武士道日志](http://kata-log.rocks/banking-kata)来介绍武士道:

> 编写一个提供以下方法的类帐户 void deposit(int) void 取款(int) String printStatement()

一个示例语句是:

```
Date Amount Balance
24.12.2015 +500 500
23.8.2016 -100 400 
```

Enter fullscreen mode Exit fullscreen mode

## 类型

`void deposit(int)`和`void withdraw(int)`是不纯函数。事实上，他们接受一个`int`并返回`void`。他们能做任何有用的事情的唯一方法是改变一些状态。

`String printStatement()`也不纯洁。事实上，它会无中生有地返回一个字符串。它做任何有用的事情的唯一方法是访问某个状态。在这篇文章中，我将把`printStatement`当作`void printStatement()`来实现。也就是说，该函数将在控制台中打印该语句。原因是不知道要不然怎么编码。

在 PureScript 中读/写状态的一种方法是使用[状态单子转换器](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State.Trans#t:StateT) ( `StateT`)。

因此，我们将使用以下类型:

```
deposit :: Int -> StateT (Array Transaction) Effect Unit

withdraw :: Int -> StateT (Array Transaction) Effect Unit

printStatement :: StateT (Array Transaction) Effect Unit 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，我们的三个函数将在`StateT (Array Transaction) Effect Unit`环境中发挥作用。更简单地说，每个函数将能够操作一组事务(状态)，写入控制台或获取日期时间(`Effect`中的一元操作)，并最终不返回任何内容(`Unit`)。

这里我们有`Transaction` :
的类型

```
data Transaction
 = Deposit Info
 | Withdraw Info

type Info =
 { timestamp :: DateTime
 , amount :: Int
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 实现

先说`deposit` :

```
deposit :: Int -> StateT (Array Transaction) Effect Unit
deposit amount = do
 ts <- lift nowDateTime
 let t = Deposit { timestamp: ts, amount: amount }
 modify_ \ts -> ts <> [t] 
```

Enter fullscreen mode Exit fullscreen mode

因为我们处于一元环境(`StateT (Array Transaction) Effect Unit`)，所以我们用一个`do`来打开函数。

然后我们使用`nowDateTime :: Effect DateTime`来获取当前的日期时间。这里唯一的问题是我们需要首先在`StateT (Array Transaction) Effect Unit`到达`lift nowDateTime`。这是因为在`do`块中，每个一元操作(即非`let`操作)都必须使用相同的一元。在这种情况下，这意味着`lift nowDateTime`和`modify_ \ts -> ts <> [t]`都有类型`StateT (Array Transaction) Effect a`。

之后，`timestamp`和`amount`正确的存款被分配给`t`。

最后， [`modify_`](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State#v:modify_) 用于通过追加新的事务`t`来访问当前状态`ts`(事务数组)。

`withdraw`差不多:

```
withdraw :: Int -> StateT (Array Transaction) Effect Unit
withdraw amount = do
 ts <- lift nowDateTime
 let t = Withdraw { timestamp: ts, amount: amount }
 modify_ \ts -> ts <> [t] 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有`printStatement` :

```
printStatement :: StateT (Array Transaction) Effect Unit
printStatement = do
 s <- gets toStatement
 lift $ log s 
```

Enter fullscreen mode Exit fullscreen mode

第一行使用 [`gets`](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State#v:gets) 获取状态(事务数组)并通过`toStatement :: Array Transaction -> String`运行它。这意味着`gets toStatement`有类型`Effect String`，而`s`有类型`String`。

最后一条线在`StateT (Array Transaction) Effect Unit`处提升`log s :: Effect Unit`。换句话说，它将`s`打印到控制台。

`toStatement`的实现没那么重要。这里有一个例子:

```
toStatement :: Array Transaction -> String
toStatement =
 fst <<< foldl fnc (Tuple "" 0)
 where
 fnc (Tuple s i) (Deposit d) =
 Tuple (s <> "\n" <> joinWith " " [show d.timestamp, show d.amount, show $ i + d.amount]) (i + d.amount)
 fnc (Tuple s i) (Withdraw w) =
 Tuple (s <> "\n" <> joinWith " " [show w.timestamp, "-" <> show w.amount, show $ i - w.amount]) (i - w.amount) 
```

Enter fullscreen mode Exit fullscreen mode

## 点火吧

现在我们可以写类似于
的东西

```
do
 deposit 500
 withdraw 100
 printStatement 
```

Enter fullscreen mode Exit fullscreen mode

其类型为`StateT (Array Transaction) Effect Unit`。我们可以用 [`evalStateT`](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State.Trans#v:evalStateT) 来运行计算。注意，下面的代码返回了`Effect Unit`。

```
flip evalStateT [] do
 deposit 500
 withdraw 100
 printStatement 
```

Enter fullscreen mode Exit fullscreen mode

## 显示代码

这里我们有所有的代码

```
data Transaction
  = Deposit Info
  | Withdraw Info

type Info =
  { timestamp :: DateTime
  , amount    :: Int
  }

deposit :: Int -> StateT (Array Transaction) Effect Unit
deposit amount = do
  ts <- lift nowDateTime
  let t = Deposit { timestamp: ts, amount: amount }
  modify_ \ts -> ts <> [t]

withdraw :: Int -> StateT (Array Transaction) Effect Unit
withdraw amount = do
  ts <- lift nowDateTime
  let t = Withdraw { timestamp: ts, amount: amount }
  modify_ \ts -> ts <> [t]

printStatement :: StateT (Array Transaction) Effect Unit
printStatement = do
  s <- gets toStatement
  lift $ log s

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
    deposit 500
    withdraw 100
    printStatement 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

如果你喜欢这个帖子，并想帮助传播这个消息，请发出一些声音🤘但前提是你真的喜欢它。否则，请随时评论或发推特给我任何建议或反馈。

感谢利亚姆·格里芬(Liam Griffin)启发我用纯脚本的方式尝试这个练习，而[在 Haskell](https://medium.com/@Gryff/bank-kata-in-haskell-dealing-with-state-3364c13b994f) 的帖子。

最后，我想对 BusConf 和所有支持我的 PureScript 之旅的人大声疾呼。你真棒！

如果你渴望更多，看看我们如何在后续测试代码:[在 PureScript 中测试银行形](https://dev.to/riccardoodone/testing-bank-kata-in-purescript-16k8)。

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！