# 单子叶转化体、mtl 和 mtl 型技术的区别

> 原文：<https://dev.to/rinn7e/dead-simple-explanation-of-the-difference-between-monad-transfomer-mtl-and-mtl-style-technique-5cf1>

这篇文章旨在解释这三者之间的区别。这三个术语经常出现，区分它们非常重要。

### 要求

这篇文章的目标读者是已经学习了基本概念(单子，函子...)并已经编写了一些 haskell 程序。

# 单子变压器

`monad-transformer`是一个类型容器，如`ReaderT`、`WriterT`...我们将只关注用例。

当人们提到`monad-transformer`时，他们通常指的是他们的`monad-transformer`栈:`newtype AppM a = ReaderT Env (LoggingT IO) a`

haskell 应用程序之所以只使用这个堆栈，是因为`monad-transformer`提供了额外的功能，例如`ReaderT`提供了`ask`函数来获取 env 变量，而不是在所有函数中传递它。

# MTL -单子变压器库

库定义了各种帮助 monad-transformer 工作的类型类。记住`mtl`是一个库而不是一个`monad-transformer`。

之所以创建 mtl，是因为当我们定义一个使用`monad-transformer`栈的函数时，我们不想在函数类型中使用`AppM`。

例如:

*   而不是这个`askEnvAndReadFile :: FilePath -> AppM String`
*   我们想用这个`askEnvAndReadFile :: (MonadReader Env m, MonadIO m) -> FilePath -> m String`

MonadReader 和 MonadIO 是表明函数可以请求 Env 并可以执行 IO 操作的类型类。这个函数可以在任何包含 ReaderT 和 IO`monad-trasnformer`栈中使用，而不仅仅是我们预定义的 AppM。

# MTL 式(无标签决赛)

有时当 mtl 被讨论时，它不是关于 mtl 库本身，而是 mtl 库使用的技术，也就是无标签的最终版本。我不会对此提供详细解释，但基本上这是一种帮助 haskell 程序员编写具有业务模型约束的函数的技术。

例如，我们想定义一个可以管理某些资源的函数(通过 api 调用来获取资源)。我们不希望该函数能够执行任何 IO 操作。下面显示了一个只能进行 http 调用和日志记录的函数，不能进行任何其他操作

```
app :: (ManageResource m, HasLogging m) -> m ()
app = do
    result <- makeApiCall
    log (show result)
    pure () 
```

注意`ManageResource`和`HasLogging`是一个与 mtl 库无关的类型类，而是我们自己定义的:

```
class (Monad m) => ManageResource m where
  makeApiCall :: m Resource 
```

这只是一个接口，实现`makeApiCallIO`将有`MonadIO`约束或返回 IO 单子:`makeApiCallIO :: IO Resource)`。
所以当我们想在 stack AppM 中使用这个函数时，我们可以为这个`ManageResource` typeclass 定义一个实例。

```
instance ManageResource AppM where
  makeApiCall = makeApiCallIO 
```

使用 typeclass 约束而不是具体类型如`AppM`的有用之处在于，我们可以使用一个模拟堆栈 MockM，在这里我们实际上不做任何 IO 动作。

示例:

```
makeApiCallMock :: m Resource
makeApiCallMock = pure someMockData

instance ManageResource MockM where
  makeApiCall = makeApiCallMock 
```

## 结论

正如我们所见，这三个术语是不同的。最重要的是在真实世界 haskell 应用程序中大量使用的`mtl-style`技术(HasDatabase，HasLogging...).能够阅读其他人的约束类型类以及创建我们自己的约束类型类是很重要的。我以前也对这三个术语感到困惑，希望这能消除一些误解。

### 明细来源:

1.  [https://serokell.io/blog/2018/12/07/tagless-final](https://serokell.io/blog/2018/12/07/tagless-final)
2.  [https://ocharles . org . uk/posts/2016-01-26-transformers-free-monads-MTL-laws . html](https://ocharles.org.uk/posts/2016-01-26-transformers-free-monads-mtl-laws.html)