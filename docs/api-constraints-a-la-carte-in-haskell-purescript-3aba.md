# Haskell 和 PureScript 中的 API 约束

> 原文：<https://dev.to/gonzooo/api-constraints-a-la-carte-in-haskell-purescript-3aba>

这篇文章中的例子在 PureScript 中也是 100%兼容的，除了在 PureScript 中被称为`Effect`的效果单子。你所要做的就是用`Effect`替换`IO`，并使用那里可用的库。

我想每个在 Haskell & PureScript 中或周围的人都听说过这样的话:“无论如何，你编写的每一个程序最后总是有一堆`IO ()`在里面。”他们不一定是错的。抱怨的是，你有这个简洁的类型系统，应该约束你的效果，但你得到的是`IO`或`Effect`，它只是简单地允许一切。

这不是不真实的，但也不完全是故事的全部。考虑下面的函数:

```
downloadFile' :: Link -> IO (Response ByteString)
downloadFile' (Link link) =
  Http.get link `Exception.catch`
  (\(HttpExceptionRequest req (StatusCodeException resp bytestring)) -> do
     case resp ^. responseStatus . statusCode of
       404 -> putStrLn $ "ERROR: File not found (404) for " <> link
       code ->
         putStrLn $ "ERROR: Unknown error with code " <> show code <> " for " <>
         link
     pure $ fmap (const (LBS.fromStrict bytestring)) resp) 
```

Enter fullscreen mode Exit fullscreen mode

我们当然可以看到我们正在尝试下载一个文件。但是这里发生了很多与下载文件无关的事情。总的来说，我们正在处理的影响有:

*   我们正在使用 HTTP 函数:`Http.get`
*   我们正在处理异常:`Exception.catch`
*   我们正在打印到终端:`putStrLn`

我们怎样才能在我们的类型中更清楚地知道我们正在以一种轻量级的方式做什么？

好吧，我们来做一些*约束* :

```
class MonadTerminalIO m where
  putStrLnM :: String -> m ()

instance MonadTerminalIO IO where
  putStrLnM = putStrLn

class MonadHttp m where
  httpGetM :: String -> m (Response LBS.ByteString)

instance MonadHttp IO where
  httpGetM = Http.get 
```

Enter fullscreen mode Exit fullscreen mode

已经有一个关联的类型类/约束，所以我们不需要创建它。

我们现在可以将我们的函数转换成如下:

```
downloadFile ::
     (MonadHttp m, MonadTerminalIO m, Exception.MonadCatch m)
  => Link
  -> m (Response ByteString)
downloadFile (Link link) =
  httpGetM link `Exception.catch`
  (\(HttpExceptionRequest req (StatusCodeException resp bytestring)) -> do
     case resp ^. responseStatus . statusCode of
       404 -> putStrLnM $ "ERROR: File not found (404) for " <> link
       code ->
         putStrLnM $ "ERROR: Unknown error with code " <> show code <> " for " <>
         link
     pure $ fmap (const (LBS.fromStrict bytestring)) resp) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在对我们在类型签名中所做的事情更加清楚了，它只需要几个类型类和一个*泛型*返回单子类型。

因为我们现在返回`m (Response ByteString)`并且对`m`有一组约束，我们已经保证在我们的函数中不能有任何随机的`IO`或其他效果，因为那些事实上对类型系统可以想象的任何`m`都是无效的。例如，当我们试图添加可以与网络对话的东西时，它必须有一个名为`MonadNetwork`的相关约束，我们必须将它添加到我们的约束中，以使这些功能在该范围内可用。

如果我们发现自己想要更好的类型签名，它们可能只是一些小的约束/类型类。这是一种非常有效的方法来限制一个函数的能力，并且非常清楚它内部发生了什么。

这一切都是可能的，因为类型类作为泛型类型变量的约束，以及更高级的类型，允许我们一般性地谈论类型包装类型，它们一起形成了我真正喜欢的 Haskell & PureScript。