# 我看不懂 Data61 fp 课程的 twiceOptional 函数。

> 原文：<https://dev.to/antonrich/i-cant-understand-the-twiceoptional-function-from-data61-fp-course-e6h>

```
-- Here's what we've got with Optional

data Optional a
  = Full a
  | Empty
  deriving Show (Eq, Show)

mapOptional :: (a -> b) -> Optinoal a -> Optional b
mapOptional _ Empty = Empty
mapOptional f (Full a) = Full (f a)

bindOptional :: (a -> Optional b) -> Optional a -> Optional b
bindOptional _ Empty = Empty
bindOptional f (Full a) = f a

applyOptional :: Optional (a -> b) -> Optional a -> Optional b
applyOptional f a = bindOptional (\f' -> mapOptional f' a) f

twiceOptional :: (a -> b -> c) -> Optional a -> Optional b -> Optional c
twiceOptional f = applyOptional . mapOptional f

-- | Convert a list of optional values to an optional list of values.
--
-- * If the list contains all `Full` values, 
-- then return `Full` list of values.
--
-- * If the list contains one or more `Empty` values,
-- then return `Empty`.
--
-- * The only time `Empty` is returned is
-- when the list contains one or more `Empty` values.
--
-- >>> seqOptional (Full 1 :. Full 10 :. Nil)
-- Full [1,10]
--
-- >>> seqOptional Nil
-- Full []
--
-- >>> seqOptional (Full 1 :. Full 10 :. Empty :. Nil)
-- Empty
--
-- >>> seqOptional (Empty :. map Full infinity)
-- Empty
seqOptional ::
  List (Optional a)
  -> Optional (List a)
seqOptional =
  foldRight (twiceOptional (:.)) (Full Nil) 
```

我不明白的事情:

1.  我们向 twice 函数传递三个参数。

```
twiceOptional :: (a -> b -> c) -> Optional a -> Optional b -> Optional c
twiceOptional f = applyOptional . mapOptional f 
```

另一方面，mapOptional 只接受两个参数。为什么 mapOptional 不生气？这里的部分应用程序是这样工作的:

```
twiceOptional (+) (Full 1) (Full 2) = applyOptional . mapOptional f (Full 1) (Full 2) 
```

2.

```
>>> seqOptional (Empty :. map Full infinity)
Empty 
```

seqOptional 显示当遇到第一个空值时将停止执行。为什么它会停止执行？

附注[课程本身的链接](https://github.com/data61/fp-course)