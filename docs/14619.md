# Ketting 2.5 版本

> 原文：<https://dev.to/evert/ketting-25-release-2d0d>

Ketting 试图为 javascript 制作一个通用的超媒体 API 客户端。它使用“链接”和后续链接作为最重要的原语。

上周我在 [NPM](https://www.npmjs.com/package/ketting) 上发布了 2.5.0 版本，所以也许是时候按照从最有趣到最不有趣的顺序列出自[上次更新](https://dev.to/ketting-2-3/)以来的一些改进了。

## JSON:API 支持

JSON:API 是一个制作基于 JSON 的 API 的标准，它对超媒体思想也有一些支持。

特别是，该标准有一个`links`对象，它还定义了集合和集合成员之间的关系。

这个版本现在附带了一个 JSON API 的“表示器”，这意味着您现在可以这样做:

```
const myResource = await home
  .follow('next') // This might be a HTML5 document
  .follow('author') // This might be a HAL document
  .follow('about') // This might be a JSON:API document 
```

Enter fullscreen mode Exit fullscreen mode

所有这些现在将是无缝的，不管这些跳中的任何一个使用哪种格式，只有当你`get()`和`put()`格式再次相关时。

很多帮助来自 JSON:API 社区成员，比如 Gabe Sullice 和 Ethan Resnick。如果你对关于 JSON:API 如何映射到 HATEOAS 的完整讨论感兴趣，请阅读[Github 问题](https://github.com/evert/ketting/issues/109)。

## `go()`功能

不是每个端点都有到另一个资源的适当链接。有时候你只需要利用你所拥有的，自己去获取资源。

`go()`函数是一个存在于资源上的函数，它只是根据你传递的 url 给你一个新的资源。url 可以是相对的，并将基于“当前”资源进行解析。

```
const anotherResource = resource.go('?q=hello'); 
```

Enter fullscreen mode Exit fullscreen mode

## 资源是通用的

如果您对这个包使用 typescript，可能需要进行一些输入。

为此添加良好的支持将是一个持续的过程，但作为第一步,`Resource`类现在是通用的。

这意味着你可以定义一个函数，比如:

```
function foo(): Resource<MyBodyType> {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，Typescript 将使用`MyBodyType`作为从`get()`和`refresh()`返回的类型，并要求传递给`put()`的值具有该类型。

默认情况下，这是`any`。

我认为这是很好的第一步，但我也认为这需要继续发展。

## esModuleInterop 为假

Typescript 构建现在已经关闭了`esModuleInterop`。显然，将它设置为`true`会导致任何使用这个库并将这个设置设置为`false`的人出错。

所以为了不强迫人们改变这个设置，`esModuleInterop`现在是关闭的，这意味着该库将不管你自己的偏好而工作。