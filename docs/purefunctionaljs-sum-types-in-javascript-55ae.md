# JavaScript 中的 purefunctionjs-Sum 类型

> 原文：<https://dev.to/phenax/purefunctionaljs-sum-types-in-javascript-55ae>

JavaScript 在大多数开发者社区中名声很差。以至于标题中的“编写更干净、更安全的 JavaScript…”对大多数读者来说可能已经有点多余了。这很大程度上与松散的类型、无效的值和可怕的三进制表达式链(或者但愿不会如此，if 语句)有关。使用 JavaScript，所有的函数都基于“假装你得到的就是你想要的”这个概念。如果这个值对你正在运行的操作无效，那就太糟糕了。为了避免这种情况，您可以编写运行时验证、添加类型检查等，但是问题的核心仍然没有得到很大程度的解决。

* * *

## 让我们向哈斯克尔学习

像 Haskell 这样的函数式编程语言对此有明确的解决方案。求和类型！Sum 类型(或标记并集)是一种代数数据类型，它描述可以采用几种不同类型的值。haskell 中常见的例子是 Maybe monad(用于处理值的有效性)和要么 monad(用于错误处理)。

[![lost your monads?](img/0d09814b0d64b5fa8c79bd95e45baf89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1KCoSKAE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://phenax.github.io/img/blog/enum-fp/haskell.jpeg)

别担心。可能，我也对单子一无所知(看到我在那做的了吧？).我们只需要知道一个 Sum 类型有多个命名的构造函数。

在 Haskell 中，Sum 类型看起来像这样-

```
data MetalGenre = ProgMetal | DeathCore

scream :: MetalGenre -> IO ()
scream genre = putStrLn $ case genre of
  ProgMetal -> "Oh!"
  DeathCore -> "BleaAaAHHH!!"

main :: IO()
main = scream ProgMetal 
```

Enter fullscreen mode Exit fullscreen mode

这里，MetalGenre 是类型，ProgMetal、DeathCore 是该类型的构造函数。

在函数世界中，一个非常流行和有用的 Sum 类型的例子是 Maybe 类型。在 Haskell 中，Maybe 是一个 monad，它包装一个值并帮助您确保无效值不会被处理，从而允许您编写更安全的函数。

这就是 Haskell 中 Maybe 的定义的样子-

```
data Maybe = Just a | Nothing 
```

Enter fullscreen mode Exit fullscreen mode

在这里，所有的有效值都被包装在一个 Just 中，所有的无效值都被包装在一个 Nothing 中。这样，我们可以用一种简洁优雅的方式处理无效值，并确保只为有效值调用函数。

您可能会想，“但是这难道不是因为 Haskell 是静态类型的美人，而 JavaScript 是撒旦的后代吗？”。也许不是。(这个笑话已经过时了)

* * *

## 枚举 FP

不要脸的自插警报！

我有一个图书馆可以帮忙！(每个 JavaScript 开发人员都这么说)。

[EnumFP](https://github.com/phenax/enum-fp) (PRs 欢迎)

EnumFP 是一种在 JavaScript 中创建 Sum 类型的简单而轻量级的方法。受 Haskell 的魅力的启发，这个库是在考虑安全性的情况下编写的。

这是使用 EnumFP 的金属流派示例的外观。

```
const MetalGenre = Enum(['ProgMetal', 'DeathCore'])

const scream = compose(console.log, MetalGenre.cata({
  ProgMetal: () => "Oh!",
  DeathCore: () => "BleaAaAHHH!!",
}));

scream(MetalGenre.ProgMetal()); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 也许，也许？

“可能做什么”的概念比实现本身更重要。包含一个值，允许你在容器上执行一组操作，而不用担心输入的有效性。

您可以使用 EnumFP 实现一个简单的 Maybe 和几个实用函数。EnumFP 还允许您添加参数描述。这个例子使用了函数的 caseOf，它类似于 match，但是用于部分应用)。

```
const Maybe = Enum({ Just: ['value'], Nothing: [] });

// fmap :: (a -> b) -> Maybe a -> Maybe b
const fmap = fn => Maybe.cata({
  Just: compose(Maybe.Just, fn),
  Nothing: Maybe.Nothing,
});

// mjoin :: Maybe Maybe a -> Maybe a
const mjoin = Maybe.cata({
  Just: x => x,
  Nothing: Maybe.Nothing,
});

// chain :: (a -> Maybe b) -> Maybe a -> Maybe b
const chain = fn => compose(mjoin, fmap(fn)); 
```

Enter fullscreen mode Exit fullscreen mode

这里，

**fmap** 返回一个新的 Maybe 并在里面的值上运行函数，以防 Just 并忽略一个 Nothing。(比如 Array.prototype.map)

**mjoin** 解开一个给定的嵌套 Maybe。因为许多单子，比如 Maybe，对于里面的值是不可知的，你可以把单子放在另一个单子里面(她是这么说的)(比如 Array.prototype.flatten)

**chain** 映射到 Maybe 上，然后展平嵌套的 Maybe。(比如 Array.prototype.flatMap)。

让我们使用它来编写一个函数，该函数接受一个用户实例并给出您的名字，而不会抛出无效用户或无效名称的错误。

```
// head :: [a] -> Maybe a
const head = arr => (arr.length ? Maybe.Just(arr[0]) : Maybe.Nothing());

// prop :: String -> Object a -> Maybe a
const prop = key => obj => key in obj ? Maybe.Just(obj[key]) : Maybe.Nothing();

// trim :: String -> String
const trim = str => str.trim();

// split :: String -> String -> [String]
const split = seperator => str => str.split(seperator);

// safeUser :: User -> Maybe User
const safeUser = user => isUserValid(user) ? Maybe.Just(user) : Maybe.Nothing();

// getFirstName :: User -> Maybe String
const getFirstName = compose(
    chain(head), // Maybe [String] -> Maybe String
    fmap(split(' ')), // Maybe String -> Maybe [String]
    fmap(trim), // Maybe String -> Maybe String
    chain(prop('name')), // Maybe User -> Maybe String
    safeUser, // User -> Maybe User
);

Maybe.match(getFirstName(user), {
    Just: name => console.log('My name is', name),
    Nothing: () => console.log('Whats in a name?'),
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们首先通过将用户包装在一个 Maybe 中，将其转换为安全用户。然后我们使用 prop 函数获得用户名。这里的 prop 和 head 函数不是返回值，而是将值包装在一个 Maybe 中。这就是为什么映射它，然后打开它，我们使用链，而不是 fmap。

* * *

## 与 React 一起工作

是的，EnumFP 和 react 配合得很好！(从一个人口过多的社区跳到下一个社区)。

随着 16.8 中新的 react 钩子的引入，在这里不提它是一种罪过。EnumFP 附带了一个 useReducer 钩子，它是 react 的 useReducer 的简单包装器。

不想升级到 16.8？你还看《宋飞正传》的重播吗？想等你的孙子帮你升级…还有…走路？

别担心。还有一个特设可用。

您可以在这里找到更多关于与 react 集成的信息。

并且这不仅限于组件状态 tho。您可以使用 Sum 类型来处理任何类型的可枚举状态值。从处理任何操作的成功、挂起和失败状态，到包含基于其类型或有效性的值。Sum 类型在这里清理所有这些。

* * *

## 结论

这只是冰山一角。在函数式编程的世界中，隐藏着更多这些令人惊奇的想法，等待着转移到其他语言中去。成为 JavaScript 社区的一员让我意识到这并不全是坏事。我们在语言特性和标准库方面所缺乏的，我们在各种各样的库中进行了弥补，只需要安装一个 npm，强大的社区一直在为“让 JS 再次伟大”而努力。所以让我们一起在我们和坏代码之间筑起一堵墙。Covfefe。

[![Covfefe](img/aa2c0d9ef2749545a8826b9a56dae41e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a3wTLDND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://phenax.github.io/img/blog/enum-fp/trump.jpg)