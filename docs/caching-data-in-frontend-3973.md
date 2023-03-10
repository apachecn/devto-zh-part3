# 在前端缓存数据

> 原文：<https://dev.to/zeerorg/caching-data-in-frontend-3973>

我们将讨论如何有效地缓存发送到后端的请求，而不使用后端 HTTP 头。

在状态变化之间协调数据可能很困难，考虑这样一个应用程序，您向后端发送请求以获取要显示的帖子列表，当用户单击一个帖子时，应用程序发送另一个请求以获取该帖子数据。现在后端相当简单，所以您可以获得该帖子的精确数据，但是您还想显示下一篇帖子和上一篇帖子的标题，可能还会显示一些在用户会话期间共享的赞助商数据。在这种情况下，再次从后端请求数据是很浪费的，所以前端在这一点上有一些选择。

## 管理 Javascript 状态

前端可以跟踪将被重用的数据。将数据存储在一个全局变量中，将状态传递给更高的级别，或者使用像 [React Context](https://reactjs.org/docs/context.html) 这样的 api。这些方法都有问题，默认情况下全局变量是有害的。随着请求数量的增加，在组件之间传递数据或在 api 这样的上下文中维护数据会变得很麻烦。

## 使用状态管理框架

对于像 [redux](https://redux.js.org/) 这样的 JavaScript 状态管理框架来说，这是一个非常典型的用例。它们提供了一种管理复杂应用程序数据的方法。但是如果你和我一样，引入一个新框架的想法和学习围绕它编码的开销可能是一个令人生畏的任务。这些框架也可以在你的前端强加固执己见的设计，所以对于不熟悉的人来说，这是一个很大的承诺。

## 浏览器存储(真正的 MVP)

我们得到了最终答案，浏览器存储 api。它是由浏览器管理的键值存储。有两种类型的浏览器存储:本地和会话。这两者都提供了类似的 api，但是，虽然本地存储永远不会被清除，但会话存储会在选项卡关闭后被清除。这种方法比我们以前的方法好得多，因为它不像传递状态那样简单，也不像学习新的状态管理框架那样复杂。

浏览器存储 api 只包括两个操作，`setItem`和`getItem`，正如你可能猜到的那样，`setItem`存储给定键的值，`getItem`检索该值。我们不用自己管理状态，只需为数据提供键和值，以便以后存储和检索。

通过创建一个调用对 url 的 GET 请求并将结果作为承诺返回的函数，演示了该 api 的一个示例用法。

```
// Without caching
function FetchData(url) {
  return fetch(url).then(res => res.text())
}

// With Caching
function FetchData(url) {
  let storageData = sessionStorage.getItem(url);
  if (storageData === null) {
    return fetch(url).then(res => res.text()).then(textData => {
      sessionStorage.setItem(url, textData)
      return textData
    })
  }
  return Promise.resolve(storageData);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将提供的 url 视为我们的键，并存储提取的数据，以便从缓存中完成任何后续请求。这种方法最好的部分是它更容易理解，并且不会干扰我们的前端代码。这也是这种情况下我们问题的最佳解决方案。

[![Basic Architecture](img/fb9fad7e1ae290d0b75db76b4465fdd1.png "Browser storage api")](https://res.cloudinary.com/practicaldev/image/fetch/s--TJExNlYN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zeerorgprocessedblog.blob.core.windows.net/photos/frontend-caching.png)

缓存是优化性能和用户体验的最有效的技术之一。通过避免对后端的不必要请求，在前端存储请求数据提供了快速导航和对存储内容的更好控制。

*如果你喜欢我的帖子，请查看我的[博客](https://blog.zeerorg.site/)T3】*