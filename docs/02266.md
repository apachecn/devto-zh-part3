# 每种语言中的斐波那契

> 原文：<https://dev.to/renegadecoder94/fibonacci-in-every-language-1l46>

再一次，我认为用尽可能多的编程语言开始这些挑战会很有趣。上次，[我们尝试了各种语言的 Fizz Buzz。如果你还没有机会用你最喜欢的语言分享解决方案，那就去吧！否则，我们今天将在每种语言中处理斐波那契。](https://dev.to/renegadecoder94/fizz-buzz-in-every-language-7o3)

## 挑战

正如你们许多人可能知道的那样，斐波那契是一个经常与特定数字序列相关联的术语，其中第 n 项是前两项之和，或者更正式地说:F<sub>n</sub>= F<sub>n-1</sub>+F<sub>n-2</sub>。出于我们的目的，我们将从一对 1 开始序列:

> 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...

这个挑战的目标是用你选择的语言写一个程序，输出序列直到第 n 项。换句话说，程序应该能够像下面这样执行:

```
./fib 5 
```

Enter fullscreen mode Exit fullscreen mode

并且，输出上面的序列，直到第五项:

```
1, 1, 2, 3, 5 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢冒险，你可以试着让你的程序符合示例程序报告中概述的要求。除了输出序列，还要输出每一项的索引:

```
1: 1
2: 1
3: 2
4: 3
5: 5 
```

Enter fullscreen mode Exit fullscreen mode

这两种风格都适合这个挑战。也就是说，如果你遵循第二个惯例，将[转变为回购](https://github.com/TheRenegadeCoder/sample-programs)会容易得多！

## 解

和往常一样，我将用 Haskell 中的一个解决方案为大家揭开序幕，这个解决方案是由我们的几个贡献者([帕克·约翰森](https://github.com/auroq)和[帕纳吉奥蒂斯·乔吉亚迪斯](https://github.com/PanagiotisGeorgiadis) ):
带来的

```
module Main where

import System.Environment
import Text.Read

fibonacci :: Int -> [Integer]
fibonacci = flip (take) fibs

fibs :: [Integer]
fibs = 1 : 1 : zipWith (+) fibs (tail fibs)

headMaybe :: [a] -> Maybe a
headMaybe []     = Nothing
headMaybe (x:xs) = Just x

-- Takes a list of values and returns a list of strings in the format "ONE_BASED_INDEX: VALUE"
printWithIndex :: (Show a) => [a] -> [[Char]]
printWithIndex = zipWith (\i x -> (show i) ++ ": " ++ (show x)) [1..]

-- Prints out the first N numbers from the fibonacci sequence
-- where N equals to the first command line argument.
main :: IO ()
main = do
  args <- getArgs
  let n = headMaybe args
  case n >>= readMaybe of
    Nothing -> putStrLn "Usage: please input the count of fibonacci numbers to output"
    Just n  -> mapM_ (putStrLn) $ (printWithIndex . fibonacci) n 
```

Enter fullscreen mode Exit fullscreen mode

对于那些以前可能没有见过函数式编程的人来说，这个解决方案可能看起来相当疯狂。也就是说，我认为 Haskell 比任何基于 Lisp 的东西可读性都要好得多，所以你大概能明白它是如何工作的。

不幸的是，我们目前没有这个解决方案记录在我们的集合中，所以也许你们中的一个可以帮忙。我们将感谢支持！