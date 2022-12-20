# 函子构成，单子不构成

> 原文：<https://dev.to/riccardoodone/functors-compose-monads-do-not-4e8j>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-03-25-functors-compose-monads-do-not.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

[![](img/e190b2a9cfa642e2deb9e9913f2a46b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dnyQt-37--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQM3gGl2zlucMsox-1WNirQ.jpeg)

## 函子组成

让我们从复习`map` :
开始

```
map :: forall a b. (a -> b) -> f a -> f b 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，map 接受一个函数`a -> b`，给我们一个函数`f a -> f b`。出于这个原因，我们可以采用任意两个嵌套的函子(例如`Array`和`Maybe`)，并通过将两个`map`放在一起
来对嵌套的值运行一个函数

```
v :: Array (Maybe Int)
v = [Just 1, Nothing, Just 3]

f1 :: Int -> String
f1 = show

main :: Effect Unit
main = do
 logShow $ map (map f1) v

-- [(Just "1"),Nothing,(Just "3")] 
```

Enter fullscreen mode Exit fullscreen mode

## 单子成分

这次我们要来看看`bind` :

```
bind :: forall a b. m a -> (a -> m b) -> m b 
```

Enter fullscreen mode Exit fullscreen mode

如果我们尝试用与函子相同的方式进行组合，我们会注意到代码无法编译:

```
v :: Array (Maybe Int)
v = [Just 1, Nothing, Just 3]

f2 :: Int -> Array (Maybe String)
f2 i = [Just $ show i]

main :: Effect Unit
main = do
 logShow $ bind v (\x -> bind x f2) -- DOES NOT COMPILE!! 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是在嵌套的`bind` :

```
bind v (\x -> bind x f2)
     ^ Maybe Int
       ^ Int -> Array (Maybe String) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，`Maybe Int -> (Int -> Array (Maybe String)) -> ??`并不是`bind`所期望的:第一个参数似乎表明`m`是`Maybe`，但第二个参数似乎表明`m`是`Array`。这不会编译，因为单子`m`应该是相同的。

为了使程序编译，我们必须使用一个函数(即`maybe`)，这个函数特定于我们正在处理的单子(即`Maybe` ):

```
main :: Effect Unit
main = do
 -- logShow $ bind v (\x -> bind x f2) -- DOES NOT COMPILE!!
 logShow $ bind v (maybe (pure Nothing) f2) 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以使用`MaybeT`单子变压器:

```
v2 :: MaybeT Array Int
v2 = MaybeT [Just 1, Nothing, Just 3]

f3 :: Int -> MaybeT Array String
f3 i = MaybeT [Just $ show i]

main :: Effect Unit
main = do
 --logShow $ bind v (\x -> bind x f2)
 logShow $ bind v (maybe [Nothing] f2)
 logShow $ runMaybeT $ bind v2 f3 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

我公然从托尼·莫里斯的演讲中抄袭了这篇博文的内容。所以一定要检查一下[原创的东西](https://vimeo.com/73648150)出来！

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！