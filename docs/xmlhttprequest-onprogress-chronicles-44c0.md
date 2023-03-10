# XMLHttpRequest on progress chronicles

> 原文：<https://dev.to/mouvedia/xmlhttprequest-onprogress-chronicles-44c0>

## 序言

当你想以跨浏览器的方式实现某个东西时，你就陷入了 bugtracker 漏洞之中。在进行了一些~~令人筋疲力尽的~~彻底研究后，我感到了在`XMLHttpRequest.prototype.onprogress`上分享我的发现的冲动。

## 理——为什么取不切呢

在继续之前，我想解释一下为什么我更喜欢用`XMLHttpRequest`而不是`fetch`进行下载监控:浏览器厂商并没有一开始就提供`Response.prototype.body`，也就是说 **`fetch`最初并不支持**。

```
interface ProgressEvent : Event {
  readonly attribute boolean lengthComputable;
  readonly attribute unsigned long long loaded;
  readonly attribute unsigned long long total;
}; 
```

Enter fullscreen mode Exit fullscreen mode

并且*甚至*如果你当前的目标浏览器确实提供了可读的流，`XMLHttpRequest`将仍然是一个神秘的差异的更好的选择:当`content-length`响应头存在*但没有暴露*，`total`将被填充响应体的大小**，而不管`Access-Control-Expose-Headers`字段的值**。

## 创世纪

```
interface LSProgressEvent : Event {
  readonly attribute unsigned long position;
  readonly attribute unsigned long totalSize;
}; 
```

Enter fullscreen mode Exit fullscreen mode

它的第一个化身是由 Firefox 0.9.3 实现的！当时 [`ProgressEvent`接口](https://www.w3.org/TR/progress-events/)并不存在，所以他们依赖于鲜为人知的 [`LSProgressEvent`接口](https://www.w3.org/TR/DOM-Level-3-LS/load-save.html#LS-LSProgressEvent)；为了保持兼容性，WebKit 必须支持这两种接口，直到 Mozilla 最终放弃了后者。

```
interface XMLHttpRequest : XMLHttpRequestEventTarget {
  …
  attribute EventHandler onprogress;
  attribute EventHandler onreadystatechange;
  …
}; 
```

Enter fullscreen mode Exit fullscreen mode

对于其他浏览器，你不得不求助于`XMLHttpRequest.prototype.onreadystatechange`，它有自己的[缺点](https://www.quirksmode.org/blog/archives/2005/09/xmlhttp_notes_r_2.html)。可悲的是，在 Internet Explorer 7 [中引入的本地版本`XMLHttpRequest`并没有暴露部分结果](https://msdn.microsoft.com/en-us/windows/ms534361(v=vs.71)#readystate_interactive)。

## 浏览器的缺陷

### Mozilla

可能由于早期的实现，Gecko 驱动的浏览器有许多错误要考虑，特别是:

*   直到版本 9，不支持`onprogress`的`addEventListener`变体
*   在版本 3.5 和 8 之间，您必须依靠`onload`处理程序来弥补上一个进度事件的空洞缺失，该事件通常是由`onprogress`在到达 100%标记时触发的
*   直到版本 34，当出现一个`Content-Encoding`响应头字段时，`loaded`属性反映的是解压缩后的字节数*，而不是传输的原始字节数，如果服务器发送了一个`Content-Length`,那么一旦接收到所有数据，就会导致`loaded`超过`total`*

### 微软

Internet Explorer 8 带来了非标准的`XDomainRequest.prototype.onprogress`。因为它没有向回调传递任何参数，所以您必须从闭包内部跟踪`XDomainRequest.prototype.responseText`。我们不得不再等 3 年，直到 Internet Explorer 10 最终支持所有 XMLHttpRequest 级事件——包括进度。

### 网件/闪烁

*   如果`lengthComputable === false`——即`Content-Length`响应头缺失——`total`和`totalSize`用于返回 UINT64_MAX 而不是`0`
*   当`Content-Encoding`置位时，即使`Content-Length`为正，`total`也会错误地返回`0`

### 歌剧 12

```
interface XMLHttpRequest : XMLHttpRequestEventTarget {
  …
  void overrideMimeType(DOMString mime);
  attribute XMLHttpRequestResponseType responseType;
  …
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了使`loaded`属性相对于`total`属性准确，响应体必须被视为二进制。为此，你有两种可能:

*   将`responseType`设置为`"blob"`或`"arraybuffer"`
*   使用`overrideMimeType`篡改媒体类型

## 为什么？！

如果你想知道为什么我知道这么多关于这些怪癖的事情，这可以归结为我是 [cb-fetch](https://github.com/Mouvedia/cb-fetch/) 的维护者，这是一个**跨浏览器** HTTP 客户端，为你抽象出所有这些混乱。它的作用远不止于此，请务必查看！
T5 我的目标是在下一个版本发布前在 GitHub 上达到 **100 颗星**。

# 考古

[![student](img/5de1900a4e689ffb0da4fd1f9071b7bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i5osTyQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1i0bkux0zbmht4qlpca.jpg) 
我认为自己是一名 API 考古学家。你喜欢那种对一门学科的详尽检查吗？它属于 dev.to 吗？