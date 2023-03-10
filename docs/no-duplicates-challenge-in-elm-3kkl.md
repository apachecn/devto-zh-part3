# Elm 中的无重复挑战

> 原文：<https://dev.to/antonrich/no-duplicates-challenge-in-elm-3kkl>

[有一本 99 榆树问题书](https://johncrane.gitbooks.io/ninety-nine-elm-problems/content/)(本来是给 Prolog 的)。

写一个函数来删除列表元素的连续重复。

```
noDupes [1, 1, 2, 2, 3, 3, 3, 4, 5, 4, 4, 4, 4]
    == [1, 2, 3, 4, 5, 4] 
```

我在做错事。我是在轨道上还是完全偏离了轨道？

```
noDupes : List a -> List a
noDupes list =
    case list of
        [] -> []
        x::y::xs ->
            if x == y then
                x :: noDupes xs
            else
                x::y::xs
        x::xs ->
            if x == xs then
                x :: xs
            else
                x 
```

编译器已经给了我一个提示，我正在比较一个元素和一个列表:

```
17|             if x == xs then
                   ^^^^^^^
The left side of (==) is:

    a

But the right side is:

    List a

Different types can never be equal though! Which side is messed up?

Hint: Did you forget to add [] around it? 
```

有趣的是，我只是把 x 放在了[x]里。但是我现在有第二个也是最后一个案子要处理。