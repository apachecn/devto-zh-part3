# 哈斯克尔美元

> 原文：<https://dev.to/hemanth/-in-haskell-1aaj>

页（page 的缩写）这是来自 h3manth.com 的一个帖子。

函数组合在函数式编程中起着非常重要的作用，在 haskell 中，有了`$`操作符，事情会变得更好。

`$`又名`Application operator`帮助我们在函数中避免括号，快速类型检查显示:

```
Prelude> :t ($)
($) :: (a -> b) -> a -> b 
```

Enter fullscreen mode Exit fullscreen mode

```
($) :: forall r a (b :: TYPE r). (a -> b) -> a -> b infixr 0 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`(f x)`与`(f $ x)`相同，但是它帮助我们将`f (g (h x))`重写为`f $ g $ h $ x`

所以，假设我们有:

```
take 5 (reverse (filter odd [1..10])) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它重写为:

```
take 5 $ reverse $ filter odd $ [1..10] 
```

Enter fullscreen mode Exit fullscreen mode

函数组合操作符`.`
变得有趣起来

```
Prelude> :t (.)
(.) :: (b -> c) -> (a -> b) -> a -> c 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把`$`表达式重写为:

```
take 5 . reverse . filter odd $ [1..10] 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
(take 5 . reverse . filter odd) [1..10] 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子是将`$`和`.`与`interact`一起使用。

```
Prelude> :t (interact)
(interact) :: (String -> String) -> IO () 
```

Enter fullscreen mode Exit fullscreen mode

来自标准输入设备的^输入作为参数传递给该函数，结果字符串在标准输出设备上输出。

比方说，我们需要从标准输入中接受多个输入并将它们相加，我们可以这样做:

```
main = interact $ show . sum . map read . words 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在`Prelude`中做同样的尝试，你必须用`lines`来配合它。(最好不要用`GHCi`里的`interact`)

```
Prelude> interact $ show . sum . map read . words . head . lines 
```

Enter fullscreen mode Exit fullscreen mode

所以，要记住的是:

*   `f $ x = f x`

*   `(f . g) x = f (g x)`