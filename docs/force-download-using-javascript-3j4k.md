# 使用 JavaScript 强制下载

> 原文：<https://dev.to/mindsers/force-download-using-javascript-3j4k>

强迫浏览器(用户)下载一个文件似乎很难，因为我们没有一个本地 Javascript 函数来做这件事。我知道为什么这些功能不存在，但我仍然必须在我的应用程序中强制下载文件！例如，当我将业务逻辑放入我的前端应用程序时。

我的一个客户让我给他的应用程序添加一个奇妙的功能:下载 Excel 报表文件。我必须用 Javascript 开始下载，但这不会像一个`GET`请求那么简单。

在使用`XHR`或`fetch` API 的`GET`请求中，数据流被 Javascript 捕获并处理。那不是我想做的。浏览器要明白，我的文件必须下载到用户的电脑上，而不是在 Javascript 引擎里填充一个变量。

我发现的诀窍是使用 JavaScript 创建一个锚对象，但不在 DOM 中呈现它。意味着这个锚点只作为一个 JavaScript 变量存在(开玩笑的，是一个 JavaScript 常量)。

```
function downloadFile(data, name = 'file', type = 'text/plain') {
  const anchor = document.createElement('a')
  anchor.href = window.URL.createObjectURL(new Blob([data], { type }))
  anchor.download = name
  anchor.click()
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数获取文件的名称、类型和数据，并在用户的计算机上开始下载。您还会注意到，经典的纯文本文件只需要数据。

我们使用`data`变量为这个 Blob 构建一个`Blob`和一个[本地 URL。`data`可能是一个斑点，这不是问题。](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL)

我们使用`download`属性让浏览器知道我们希望下载的 Blob 的名称包含在`download`属性中。

最后但同样重要的是，我们必须触发锚上的 click 事件来启动它的常规行为，并让浏览器完成它的工作。

#### 改良版

我喜欢上面的`downloadFile`函数的简单性，但是我必须改进它来处理更多的生产案例。

```
function downloadFile(data, name = 'file', type = 'text/plain') {
  const { createElement } = document
  const { URL: { createObjectURL, revokeObjectURL }, setTimeout } = window

  const blob = new Blob([data], { type })
  const url = createObjectURL(blob)

  const anchor = createElement('a')
  anchor.setAttribute('href', url)
  anchor.setAttribute('download', name)
  anchor.click()

  setTimeout(() => { revokeObjectURL(url) }, 100)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个功能已经在通用浏览器的最新版本中测试过了。Internet Explorer 不是测试的浏览器的一部分。

为了帮助你，如果你必须支持 Internet Explorer，这里是我所知道的:锚的伎俩不会工作。你应该试试`navigator.msSaveOrOpenBlob`来代替。