# 获取 API 和异步还原状态

> 原文：<https://dev.to/charlesstover/the-fetch-api-and-asynchronous-redux-state-1fgk>

如果您正在构建一个复杂的 React 应用程序，您可能会使用一个后端服务和一个 API。如果您在 React 中管理状态，您可能正在使用 redux。这两个都是很好的选择，我强烈推荐您使用 React 应用程序的架构。然而，redux 的开箱即用同步状态操作并不特别兼容*异步*服务器响应，这让许多开发人员挠头。异步调用中涉及到 [*很多*状态](https://medium.com/r/?url=https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/readyState)，但是 API 调用不就是一个单一的动作吗？

我将向您介绍异步 API 调用中涉及的标准化状态，以及它们与 redux 存储的关系。

到本文结束时，您应该理解如何编写一个异步 redux 操作来处理 API 调用的每个状态。当这些状态被触发时，每个 API 调用都有相同的状态和逻辑，所以为了防止为每个异步 redux 动作复制粘贴相同的样板文件，我还将为[提供一个开源包](https://www.npmjs.com/package/fetch-action-creator)，我几乎虔诚地使用它来为您处理动作创建。

## 先决条件📍

要在 redux store 上启用异步操作，您需要应用 [redux-thunk](https://www.npmjs.com/package/redux-thunk) 中间件。

对于 API 调用，我将使用标准化的`fetch` API。如果你的目标浏览器不支持`fetch` API，我会推荐一个 [`fetch` polyfill](https://www.npmjs.com/package/whatwg-fetch) 。如果您希望能够中止 API 调用，我还推荐使用 [`AbortController` polyfill](https://www.npmjs.com/package/abortcontroller-polyfill) ，但是如果您不希望有这个特性，就不要使用。如果你更喜欢`fetch` API 的替代品，比如`axios`或`XMLHttpRequests`，它们绝对能够处理异步 Redux 状态管理，但是我的代码示例将基于`fetch` API。

## 什么是异步动作？🐌

第一步是理解您正在创建什么——不像以前的动作创建者返回一个动作对象，然后立即发送给 reducers，异步动作不是一个对象，而是一个可以立即调用的函数。该函数接受两个参数，每个参数都是一个函数。第一个是`dispatch`函数，用于分派一个动作；第二个是一个`getState`函数，用于获取当前的 Redux 状态。

```
// Synchronously add an employee.
// addEmployee("Bob");
const addEmployee = (name) => ({
  type: 'ADD_EMPLOYEE',
  name
});

// Asynchronously add an employee.
// addEmployeeAsync("Bob")
const addEmployeeAsync = (name) => {

  // Since the return value of this action creator
  //   accepts dispatch as a parameter instead of
  //   returning what is to be dispatched,
  //   I may dispatch at my leisure and as many times as I want.
  return (dispatch, getState) => {

    // I want to immediately and synchronously add the employee.
    dispatch(addEmployee(name));

    // I want to asynchronously remove the employee.
    // This is a second action in a single action creator.
    setTimeout(
      () => {
        dispatch(removeEmployee(name));
      },
      0
    );

    // I want to asynchronously re-add that employee after 5 seconds.
    // This is a third action in a single action creator.
    setTimeout(
      () => {
        dispatch(addEmployee(name));
      },
      5000
    );
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

通常，当您的动作创建者返回一个对象时，该对象被传递到您的 reducer。现在，当您的动作创建者返回函数时，redux-thunk 中间件将立即调用该函数，而不是将其传递给缩减器。这个函数可以做任何事情。与其他动作创建者不同，这个函数*不*返回动作对象。使用 dispatch 参数，您可以将操作对象分派给 reducer。手动分派它们而不是返回它们的好处是，尽管只分派了*一个*动作创建者，但是您可以根据需要分派任意多的动作，比如在 API 调用中为每个状态分派一个动作。

总之，您的组件调度一个异步动作(在本例中是`addEmployeeAsync`)。那个异步动作依次调度多个动作(`addEmployee`、`removeEmployee`，然后再`addEmployee`)。没有理由添加，删除，然后再添加。只是你在设计上自由的一个例子。

## 获取 API 的状态🎌

现在我们知道了如何创建一个可以随时间分派多个状态的动作，让我们来识别和分派一个获取请求的状态。

API 请求的第一个状态是*请求*(加载)。请求已经发出，但我们尚未收到回应。

API 请求的后续状态是*收到*(成功)或*拒绝*(错误)，这取决于服务器的响应。

API 请求的最终潜在状态是*aborted*(canceled ),如果您或用户在收到响应之前终止了请求。

对于为应用程序提供动力所需的每个 API 端点，初始 Redux 状态可能如下所示:

```
{
  "myApiData": {
    "abortController": null,
    "aborted": false,
    "error": null,
    "loading": false,
    "response": null
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您将需要针对这些状态中的每一个状态的操作，因为每个 API 请求的状态都应该反映在您的应用程序中。

```
// When the API is requested,
//   this action is sent to the reducer.
// The abortController tied to the request,
//   so passed to the request action creator.
// You may store it in your redux state for future use.
const requestMyApi = abortController => ({
  type: 'REQUEST_MY_API',
  abortController
});

// When the API responds,
//   this action is sent to the reducer.
// It includes the response, which is probably
//   the entire point of this process.
const receiveMyApi = response => ({
  type: 'RECEIVE_MY_API',
  response
});

// When the API fails to respond,
//   this action is sent to the reducer.
// The provided error is included, which can
//   be used to display to users or debug.
const rejectMyApi = err => ({
  type: 'REJECT_MY_API',
  error: err
});

// When the API request has been aborted or cancelled,
//   this action is sent to the reducer.
const abortMyApi = () => ({
  type: 'ABORT_MY_API'
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 中止动作🙅

为了让 API 请求通知开发人员它已经被取消，必须在实例化时向它传递一个 [`AbortSignal`](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) 。尽管这不是调度的第一个动作，但它将是我们编写的第一个动作，因为它必须在 API 请求初始化之前编写。

```
let abortController = null;
let signal;

// Since AbortController is not well-supported yet, we check for its existence.
if (typeof AbortController !== 'undefined') {
  abortController = new AbortController();
  signal = abortController.signal;
  signal.addEventListener('abort', () => {
    dispatch(abortMyApi());
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

如果浏览器支持，我们创建一个 [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) ，并为中止信号添加一个监听器。当中止信号事件发生时，我们调度中止动作。`AbortController`稍后将作为请求动作的一部分被传递。这允许您将它存储在 redux 状态中，让您的组件和用户能够通过控制器手动中止 API 请求。

当您的 reducer 接收到一个`ABORT_MY_API`动作时，您可以相应地操纵状态:它不再加载，没有响应，没有错误，并且被中止。如果符合您的用例，您可能更喜欢用一个错误字符串替换 aborted 标志来简化您的逻辑。然而，我建议不要这样做，因为存在这样的逻辑差异，比如“如果用户中止了前一个有效载荷，他们还能重新请求吗？如果前一个错误发生了？”

## 请求动作📞

您应该使用*请求*动作来启用加载视图。考虑使用加载动画或文本来通知你的用户*有事发生*。反馈对让你的应用程序有反应大有帮助。`REQUEST_MY_API`动作将把`state.myApi.loading`从假切换到真。您的组件现在可以相应地响应这个 redux 状态。依赖于来自我的 API 的响应的组件可以显示它们正在加载。

因为请求是立即实例化的，所以您可以在您的异步动作创建器:`dispatch(requestMyApi(abortController))`中立即调度该动作。

既然我们已经告诉 reducer 我们已经请求了数据，那么我们实际上应该请求它:`fetch(URL, { signal })`。您可以根据需要调整提取选项。`signal`是作为上面中止处理程序的一部分创建的。

这不仅仅是请求数据，我们还需要处理响应。

## 响应动作🙌

一旦 fetch Promise 解析完毕，我们就可以获取响应，对其进行相应的解析(作为文本或 JSON)，并将解析后的数据发送给 reducer，使其可以被您的组件访问。

```
fetch(URL, { signal })
  .then(response => {

    // If this payload is JSON, use this:
    return response.json();

    // If this payload is not JSON, use this:
    return response.text();
  })
  .then(data => {

    // Now that we've parsed the response,
    //   we can send it to the reducer.
    dispatch(receiveMyApi(data));
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 错误动作❌

错误动作甚至更容易。既然我们在承诺中工作，我们就`catch`！

```
fetch(URL, { signal })
  .then(parseData)
  .then(receiveMyApi)
  .then(dispatch)
  .catch(err => {

    // An error occurred at some point in this Promise.
    // Pass the error to the reducer.
    dispatch(rejectMyApi(err));
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 注意事项🤔

如果您的 API 成功地用*错误状态码*和一个错误消息作为解析的有效负载的一部分来响应，那么就涉及到更复杂的错误处理。我不会在这里详细讨论这种情况，因为它并不适用于所有的 API，但是你可以在这个包的[源代码中看到我是如何处理它的。](https://github.com/CharlesStover/fetch-action-creator/blob/master/fetch-action-creator.ts#L136)

您还拥有`getState`功能的力量。您可以使用当前的 redux 状态来修改(甚至忽略)当前的获取请求。根据场景和操作的不同，有时我会获取当前状态，以查看请求是否已经加载或在过去已经响应过。如果有，我就不拿了。异步操作显然是错误地分派的，所以我可以放心地忽略它——我已经有了数据，所以获取它对我没有任何好处。

## 这大部分不能自动化吗？🤖

是啊！ [`fetch-action-creator`包](https://www.npmjs.com/package/fetch-action-creator)做了上面所有的事情，所以你不必为每个 API 动作复制粘贴这个样板文件。每个 API 调用都将做一系列相同的事情:创建一个中止控制器和信号，获取请求，解析响应，检查响应中的错误状态代码，并为流程中涉及的四种状态中的每一种状态分派一个动作。

如果说我爱什么，那就是[干代码](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)！这就是为什么我使用并推荐一个能为你做所有这些事情的函数。剩下要做的就是提供任意两个给定 API 调用之间的差异:唯一标识符、URL 和获取选项。

就`npm install fetch-action-creator`或者`yarn add fetch-action-creator`！

## 获取-操作-创建者🐶🎾

一定要理解*动作*和*动作创作者*的区别。 [`fetch-action-creator`包](https://www.npmjs.com/package/fetch-action-creator)不返回动作创建者。它*是*一个动作创建者，所以它返回一个异步动作，意味着它返回`(dispatch, getState) => {}`函数。

你的动作创建器看起来会像这样:

```
import fetchActionCreator from 'fetch-action-creator';

export const fetchMyApi = () =>
  fetchActionCreator(
    'MY_API',
    'https://path.to/api',
    null // fetch options, if any
  ); 
```

Enter fullscreen mode Exit fullscreen mode

`'MY_API'`字符串用于生成 Redux 动作类型:`'REQUEST_MY_API'`、`'RESOLVE_MY_API'`、`'REJECT_MY_API'`、`'ABORT_MY_API'`。

您的 React 组件将只绑定和调用`fetchMyApi` action creator，在本例中它没有任何参数。

您可以使用参数来自定义您的获取操作。内联扩展您的操作需要更多的工作，但是可扩展性的回报是巨大的。

```
import fetchActionCreator from 'fetch-action-creator';

// We now call fetchAddEmployee("Bob")
const fetchAddEmployee = name =>
  fetchActionCreator(
    'ADD_EMPLOYEE',
    'https://path.to/employees',

    // POST Bob
    {
      body: name,
      method: 'POST'
    },

    // For each action, merge with object { name }
    //   to add a name property containing
    //   employee's name to the action object.
    {
      onAbort: { name },
      onReject: { name },
      onRequest: { name },
      onResolve: { name }
    }
  ); 
```

Enter fullscreen mode Exit fullscreen mode

你可以查看文档中关于改变 Redux 动作的高级选项。

`fetch-action-creator`包是[在 GitHub](https://github.com/CharlesStover/fetch-action-creator/) 上开源的。欢迎拉请求！

## 结论🔚

如果你喜欢这篇文章，请随意给它一个心形或独角兽。又快又简单，还免费！如果你有任何问题或相关评论，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。