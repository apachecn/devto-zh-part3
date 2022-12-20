# 在哪里放置响应元数据-信封或 HTTP 头？

> 原文：<https://dev.to/tiguchi/where-to-put-response-metadata---envelope-or-http-headers-4ai9>

这里有一些吹毛求疵的令人挠头的问题，我现在想得太多了。假设我们有一个 RESTful API，它带有资源收集端点，允许我们搜索并获得分页的结果。

将响应元数据(如“下一页”URL 或下一页光标令牌)放在哪里？

有两种方法可以做到。

## HTTP 响应头

这是我目前喜欢的添加元信息的方式。它可以作为 HTTP 头添加到响应中，而不影响实际的响应数据:

```
MyAPI-Next-Page: /users?next=ABCDEFG123 
```

```
[  {"id":  "user-1"},  {"id":  "user-2"}  ] 
```

## 响应信封

然而，许多 API(如脸书或 Twitter)更喜欢将响应包装在信封对象的数据字段中，并向该包装添加其他元数据:

```
{  "pagination":  {  "next":  "/users?next=ABCDEFG123"  },  "data":  [  {"id":  "user-1"},  {"id":  "user-2"}  ]  } 
```

在下面这个提出 JSON APIs 标准的网站上，它甚至被吹捧为前进的道路:[https://jsonapi.org/](https://jsonapi.org/)

## 我为什么不喜欢信封

老实说，我最近开始不喜欢他们了。几年前，我设计并实现了一个电子商务 web API。受常见 JSON API 设计实践的启发，我确实将搜索结果封装在信封中。

这是我们案例中的三个问题:

### 1。毕竟，客户端应用程序并不真正需要元数据

它们使用 infini-scroller 并简单地请求下一页，直到 API 以空结果集响应。

此外，结果项的总量或页面的数量基本上被忽略。我们不需要用这些信息来更新 UI，我们也没有实现老式的导航栏。

### 2。在 JavaScript 代码中处理响应数据有点困难

```
axios.get('/users').then(response => {
    //                              +-- really?
    //                              v
    let resultList = response.data.data; 
    // ...
}); 
```

当然，这只是一个装饰性的东西，可以通过将`data`属性重命名为其他名称(比如`results`)来轻松修复。但这也意味着破坏与我之前提到的 [JSON:API 标准](https://jsonapi.org/)的兼容性(开玩笑，我并不真的关心那个标准:-D)。

### 3。它在客户端应用程序中反复引入了同样的错误

到实际结果集`response.data.data`的额外跳转经常被忽略，这重复地导致了只有在测试时才能检测到的错误。其中一些还隐藏了一段时间。

增加的混乱可能还源于这样一个事实，即单个资源没有被包装在信封中，因此可以通过`response.data`直接访问。

### (编辑)4。这也有点多余

正如 Stackoverflow 的某个人指出的那样: [HTTP *已经是*的信封](https://stackoverflow.com/a/9999335)。确实没有必要将结果集包装在信封数据结构中。

## 那么为什么我还是很迷茫呢？

我认为，拥有大量实践经验的非常聪明的人会在脸书、Twitter 之类的公司工作。我还假设他们可能遇到了使用 HTTP 响应头向响应添加元数据的问题。

有人了解这类问题吗？我相信这可能是代理清除或改变标题的问题？还是浏览器中的 JavaScript 没有给出对所有头的完全访问？还是有奇怪的 HTTP 客户端库不知道如何解析头？

你的经验或看法是什么？