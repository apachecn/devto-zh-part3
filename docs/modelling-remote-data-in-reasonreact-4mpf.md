# 在 ReasonReact 中对远程数据建模

> 原文：<https://dev.to/margaretkrutikova/modelling-remote-data-in-reasonreact-4mpf>

让我们来看看一种非常常见的方法，用`Typescript`在 react 应用程序中为 api 数据建模。您有一个`isLoading`标志、实际的`data`和一个`error`属性，以防出现问题:

```
type State = {
  isLoading: boolean
  data: Data | null
  error: string | null
} 
```

Enter fullscreen mode Exit fullscreen mode

这种结构易于使用，但远非完美，因为它允许表示无效状态，并且在呈现特定状态的 UI 部件时容易出错(在`render`函数中有一堆`if`)。

这种错误的一个例子是将初始数据设置为一个空数组，比如通知，并在加载实际通知时忘记隐藏它。这会导致“您没有通知”消息，而它应该显示“正在加载通知”。在这篇伟大的文章[Elm 如何消除 UI 反模式](http://blog.jenkster.com/2016/06/how-elm-slays-a-ui-antipattern.html)中详细描述了这个例子。

当编写`reason`时，该语言给了我们一个坚实的类型系统，允许通过使用变量和模式匹配以更好的方式对远程数据建模。有了合适的数据模型，这种语言也将迫使我们为用户提供每个可能状态的正确反馈。

在本文中，我想展示一种为 api 数据建模状态的方法，同时避免无效状态。我将展示如何使用库 [`remotedata-re`](https://github.com/lrosa007/remotedata-re) ，最后展示如何在其上创建我们自己的抽象，以便在应用程序的不同部分重用它。

* * *

## 远程数据状态

我将要展示的解释远程数据概念的例子，来自我构建的一个迷你应用程序——一个在[api.adviceslip.com](https://api.adviceslip.com/advice)使用 api 的建议生成器。

现在你可能会想，这听起来很傻:)但玩这个应用程序真的很有趣，一些建议真的很有启发性，比如这些:

> *   Enjoy a little nonsense once in a while.
> *   You don't need to floss your teeth. Only what you want to keep.

这个应用的源代码在我的 github 上的[这个回购](https://github.com/MargaretKrutikova/advice-app-re)里。在该应用中，用户可以通过输入搜索输入来搜索建议，当用户按下`Enter`时，它会调用 api 并显示一个点击列表。

当进行 api 调用时，我们对以下状态感兴趣:

*   我没有任何数据可以显示，也没有进行 api 调用，
*   我还没有任何数据，但是我已经发送了一个 api 调用并等待响应(`loading`)，
*   我从服务器收到一个错误，
*   我收到了一些数据，可以呈现出来，
*   我正在重新获取数据，(`loading`但是显示旧数据以防止内容闪烁)。

## 用一个变体造型状态

所以我们想要表达这些状态，我们从一个[变量](https://reasonml.github.io/docs/en/variant)开始。搜索 api 响应和搜索结果可能如下所示:

```
type searchResponse = {
  total_results: int,
  items: string,
};

type searchResult =
  | NotAsked
  | Loading(option(searchResponse))
  | Failure(string)
  | Success(searchResponse) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们将数据类型限制为仅处于这些状态之一:加载可能的数据为`searchResponse`，失败为`string`，成功为`searchResponse`。

请注意，加载状态也可以保存数据，在第一次搜索之前，数据将为空，但在任何后续搜索中，都将保留先前的搜索结果。

如果加载状态没有携带任何信息，下次我们搜索时，当前的搜索结果将在下一次响应返回之前消失。在某些情况下，这可能是好的，甚至是有用的(例如，防止显示陈旧的数据)，但在这种情况下，我们不希望屏幕在调用之间不必要地跳跃。

## 使用`RemoteData`

为了重用上面的数据结构，我们可以使它多态并添加一个类型参数:

```
type apiData(a') =
  | NotAsked
  | Loading(option(a'))
  | Failure(string)
  | Success(a') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像`type searchResult = apiData(searchResponse)`一样创建我们的类型。

但是已经有一个小而方便的库叫做 [remotedata-re](https://github.com/lrosa007/remotedata-re) ，具有相似的数据类型。这个库还附带了几个实用函数来处理这个数据结构。在`RemoteData`中定义的类型看起来与我们自己的`webData` :
非常相似

```
type t('a, 'p, 'e) =
  | NotAsked
  | Loading('p)
  | Failure('e)
  | Success('a); 
```

Enter fullscreen mode Exit fullscreen mode

使用这个结构，我们可以重新定义`searchResult`，并像这样建模我们的状态:

```
type state = {
  searchResult: RemoteData.t(searchResponse, option(searchResponse), string),
};

let initialState = {searchResult: RemoteData.NotAsked}; 
```

Enter fullscreen mode Exit fullscreen mode

## 状态间的转换

当进行 api 调用时，为了在状态之间转换，我们需要定义动作，这些动作将承载关于转换的信息，以及响应这些动作的缩减器。

下面是它可能的样子:

```
type action =
  | SearchLoading
  | SearchError(string)
  | SearchSuccess(searchResponse);

let reducer = (state, action) => {
  switch (action) {
  | SearchLoading => {
      ...state,
      searchResult: RemoteData.(Loading(
          state.searchResult |> map(d => Some(d)) |> withDefault(None),
        )),
    }
  | SearchError(error) => {...state, searchResult: RemoteData.Failure(error)}
  | SearchSuccess(result) => {...state, searchResult: RemoteData.Success(result)}
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

当处理加载状态时，如果`searchResult`为`Success`，我将使用帮助函数`RemoteData.map`将函数`(d => Some(d))`应用于底层数据，而`RemoteData.withDefault`将数据从状态`Success`中“展开”，否则返回`None`。

`RemoteData.(...)`在本地打开模块，并允许引用范围内的模块值，而无需在它们前面加上前缀`RemoteData`。

## 自定义远程数据类型

通常一个有几个页面的更大的应用程序需要在不同的时间点执行不同的 api 调用。那么，在处理`Loading`、`Error`和`Success`的情况时，我们是否必须在我们的 reducer 中重复那个巨大的代码块呢？

我想避免这样做，并且作为一个实验，在一个名为`WebData`(借用 elm 包`elm-web-data`的名字)的模块中创建了一个小的抽象逻辑:

```
type t('a) = RemoteData.t('a, option('a), string);

type apiAction('a) =
  | RequestLoading
  | RequestError(string)
  | RequestSuccess('a);

let toLoading = (data: t('a)): t('a) =>
  RemoteData.(Loading(data |> map(d => Some(d)) |> withDefault(None)));

let updateWebData = (data: t('a), action: apiAction('a)): t('a) => {
  switch (action) {
  | RequestLoading => data |> toLoading
  | RequestError(error) => RemoteData.Failure(error)
  | RequestSuccess(response) => RemoteData.Success(response)
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我定义了一个多态类型，它已经将`option('a)`作为`Loading`状态。我还包含了一个状态转换的动作类型和一个处理实际转换的助手函数。

现在我们可以这样修改上面的搜索结果代码:

```
type state = {searchResult: WebData.t(searchResponse)};

type action =
  | SearchRequest(WebData.apiAction(searchResponse));

let reducer = (state, action) => {
  switch (action) {
  | SearchRequest(searchAction) => {
      searchResult: WebData.updateWebData(state.searchResult, searchAction),
    }
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个看起来干净多了！我将搜索结果的 api 动作封装在一个更具体的变体`SearchRequest`中。然后当对它进行模式匹配时，我可以提取底层的 api 动作并将其传递给函数`updateWebData`，该函数为`searchResult`返回新的状态。

这种模式受到了 Elm 架构的启发，在 Elm 架构中，您可以创建一个拥有自己的状态并公开其更新功能和消息的模块。当模块被插入到主程序中时，它的消息被包装到一个新的构造函数中，该构造函数是全局消息的一部分，然后全局更新函数可以打开它，并用模块理解的底层消息调用该模块的更新函数。

在建议生成器应用程序中，`WebData`模块被重用来获取搜索结果和生成随机建议，你可以在这里查看实现[。](https://github.com/MargaretKrutikova/advice-app-re/tree/master/src)

## 渲染远程数据

让我们看看如何匹配`state.searchResult`的所有可能状态，并在每种情况下给用户正确的反馈:

```
{switch (state.searchResult) {
  | NotAsked =>
    <Message type_=Information text="You haven't searched yet!" />
  | Loading(None) => <Spinner />
  | Success(data) => <SearchResult data />
  | Loading(Some(data)) => <> <Spinner /> <SearchResult data /> </>
  | Failure(err) => <Message type_=Error text=err />
  }} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`Message`、`Spinner`、`SearchResult`是我在 app 中定义的组件(源代码[这里的](https://github.com/MargaretKrutikova/advice-app-re/tree/master/src))。

这里有一点重复。带有`Some(data)`的`Success`和`Loading`都使用`SearchResult`来呈现数据，但是实际的呈现逻辑可能更复杂，所以我们可能希望在一种情况下处理它以避免这种重复:

```
{switch (state.searchResult) {
  | NotAsked =>
    <Message type_=Information text="You haven't searched yet!" />
  | Loading(None) => <Spinner show=true />
  | (Success(data) | Loading(Some(data))) as searchState =>
    <>
      <Spinner show={RemoteData.isLoading(searchState)} />
      <SearchResult data />
    </>
  | Failure(err) => <Message type_=Error text=err />
  }} 
```

Enter fullscreen mode Exit fullscreen mode

通过模式匹配和使用来自`remotedata-re`的帮助函数，可以有不同的方式来呈现`RemoteData`或`WebData`，并且它们很可能会随着不同的 UI 需求而变化(例如，旋转器的放置，在加载时禁用页面上的其他元素等)。).

## 结论

关键点是:

*   在`ReasonMl`中使用变量来模拟远程数据有助于避免无效状态，
*   变体中的每个构造函数代表一个 api 调用的特定状态，并且可以携带额外的信息(比如带有 api 数据的`Success`状态)，
*   是一个方便的包，它已经实现了一个远程数据类型并公开了使用它的函数，
*   您可以创建自己的可重用抽象来帮助管理整个应用程序中的 api 数据，
*   呈现远程数据包括直接在您的`jsx`中进行模式匹配，实现可能会因 UI 而异。

在 reason 中处理远程数据时，您发现了哪些有用的模式？很想听听你的经历，并感谢在评论中分享你的经历:)