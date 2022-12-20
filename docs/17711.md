# medfetch

> 原文：<https://dev.to/remy/memfetch-3n7f>

web API `fetch`非常简单(与它的前身`XMLHttpRequest`相比)，但是在开发过程中，当发出获取请求时，我总是有一些顾虑:触及第三方请求限制、延迟(因为我希望开发快速)和离线的可能性(又名:在火车上开发)。

由于浏览器是一个美丽的东西，我能够用我自己的定制逻辑包装`fetch` API 并规避这些顾虑，所以我向你介绍: [memfetch](https://github.com/remy/memfetch)

## 它有什么作用

Memfetch 将悄悄地包装 fetch API，并缓存*任何*请求和响应，以便如果您再次收到**完全相同的**请求，它将从本地浏览器缓存中得到服务。

重要的是，没有对我的代码进行任何修改来利用请求的缓存版本。这方面对我来说是一个重要的特性，因为我把 memfetch 看作是一个开发工具，我应该能够在生产之前删除它，而不必重构代码。

## 如何使用

同样，我的 JavaScript fetch 请求没有改变，只是在我的代码运行之前加入了 memfetch 库:

```
<script src="https://unpkg.com/memfetch"></script> 
```

现在，所有的获取请求都将被缓存，后续的请求将直接从本地浏览器的存储中处理。重要的是，使用传递给`fetch`的 url 和 init 选项对请求进行指纹识别。这意味着如果有一个额外的选项传递给你的请求(比如添加`mode: 'cors'`)，memfetch 会把它视为 distinct。

同样，*所有的*获取请求都被缓存，这包括`POST`请求。我怀疑将来如果有需求的话，我会有某种`include` / `exclude`的配置。

为了清除本地缓存，`fetch`方法中有一个`seed`属性，如果它被设置为与当前值不同的值(即从`undefined`更改为`a`或从`a`更改为`b`，本地缓存将被完全清除:

## 引擎盖下

最初，这个库会包装`fetch` API 并返回一个`new Proxy`来允许 memfetch 捕获代码使用的`.json`方法或`.blob`方法——但结果是我可以简化代码并放弃使用代理(尽管它们很酷)。

最初的`fetch`方法存储在一个内部方法中，并用一个包装器替换。

当你的代码发出一个请求时，下面的内容被存储在 IndexedDB 中(感谢杰克·阿奇博尔德的微型 idb-keyval 库):

*   全球资源定位器(Uniform Resource Locator)
*   获取选项
*   响应标题
*   状态
*   statusText
*   一滴

使用这些值，memfetch 构造一个`new Response`并返回那个而不是原始响应的*。*

后续请求检查是否有缓存的值，并使用捕获的数据点(如上)，用另一个`new Response`进行解析——这样编写的代码可以调用`.blob`或`.json`或任何其他由响应 API 实现的方法(这意味着 memfetch 不需要使用代理对象)。

*原载于[雷米夏普的 b:log](https://remysharp.com/2018/12/24/memfetch)T3】*