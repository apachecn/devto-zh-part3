# Purescript Halogen 内部的全局消息传递

> 原文：<https://dev.to/rinn7e/global-message-passing-inside-purescript-halogen-30ol>

通常在卤素路由器组件中，您会有一个`Query`来像这样改变路由:

```
-- Router.purs
Goto page n -> do
    S.log $ show page
    handlePage page
    pure n
HandleHeroListPage msg n -> do
    case msg of
        HeroList.ChangeRoute page -> do
            handlePage page
            pure n
        _ ->
            pure n 
```

handlePage 函数如下所示:

```
-- Router.purs
handlePage :: Page -> _
handlePage page = do
    currentPage <- H.gets (\s -> s.currentPage)
    case (currentPage == page) of
    true ->
        pure unit
    false -> do
        H.modify_ (_ { currentPage = page })
        pure unit 
```

这样做的问题是，当在路由器组件中使用时，我们必须调用`HE.onClick $ HE.input_ HeroListPage`来改变路由，或者当在子组件中使用时，我们将把消息传递回父组件`H.raise $ ChangeRoute HeroListPage`，然后我们像上面的`HandleHeroListPage`一样处理它。

当通过深层嵌套组件更改路由时，我们必须将某种消息传递回顶层组件(路由器组件)进行处理，这可能非常烦人。

一个更快的方法是简单地用`href="/#/home"`改变路线，但是使用散列风格的 url 对于搜索引擎和 google analytic 来说是有问题的。

使用不带哈希的 href 会重新加载页面，我们不希望这样。这就是为什么我们必须通过手动使用 pushState 来改变 url。

全局传递消息在这种情况下非常方便，我们可以通过在 AppM ReaderT 模式中使用 Avar，从任何子组件直接向路由器组件传递消息。

我们的 AppM 将如下所示:

```
-- AppM.purs
newtype AppM a = AppM (ReaderT Env Aff a)

runAppM :: forall a. Env -> AppM a -> Aff a
runAppM e (AppM m) = runReaderT m e

-- Data.Env.purs
data GlobalMessage 
    = LogoutG 
    | NavigateG Page

type Env = 
    { globalMessage :: AVar GlobalMessage
    , pushStateInterface :: PushStateInterface
    }

-- Main.purs
main :: Effect Unit
main = do
    log "Purescript is starting..."
    HA.runHalogenAff do
        globalMessage <- AVar.empty
        pushStateInterface <- liftEffect $ RoutingP.makeInterface
        let 
            environment :: Env
            environment = { pushStateInterface, globalMessage}

            rootComponent :: H.Component HH.HTML Router.Query Router.Input Void Aff
            rootComponent = H.hoist (runAppM environment) Router.component
-- ... 
```

在我们的路由器内部，我们将分叉一个监听 AVar 变化的查询

```
Init n -> do
    S.log "Router Initialized"
    void $ H.fork $ eval $ ListenForGlobalMessage n
    pure n
ListenForGlobalMessage n -> do
    globalMessage <- asks _.globalMessage
    query <- H.liftAff $ AVar.take globalMessage
    case query of
    NavigateG page -> do
        handlePage page
        pure unit
    _-> do
        pure unit
    eval (ListenForGlobalMessage n) 
```

在任何子组件中，我们调用`raiseG`,这基本上将一个新的全局消息放入 avar 中

```
raiseG query = do
  globalMessage <- asks _.globalMessage
  H.liftAff $ AVar.put query globalMessage 
```

例如，在一个名为 HeroList 的子组件中，我们只需调用 raiseG，将我们想要传递的消息直接传递给路由器组件

```
GotoHeroDetail n -> do
    S.raiseG $ NavigateG $ HeroDetailPage 1
    pure n 
```

就这样，你可以看看我的 haloge starter 项目中的完整工作示例:[https://github.com/rinn7e/rinn7e-halogen-starter](https://github.com/rinn7e/rinn7e-halogen-starter)

感谢阅读！