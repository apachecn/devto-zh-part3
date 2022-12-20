# 快速提示—使用 AbortController

> 原文：<https://dev.to/gilfink/quick-tip-working-with-abortcontroller-23o3>

### 快速提示—使用堕胎控制器

在某些情况下，您可能希望取消服务器请求。例如，您的用户正在请求一个需要花费大量时间来检索的报告，并且改变了主意，请求另一个报告。你会怎么做？这就是**人工控制器**非常方便的地方。

在这篇文章中，我将简要解释什么是**中止控制器**以及如何使用它中止获取请求。

#### 请停止抓取

**AbortController** 是一个可以帮助你中止获取请求的对象。你是怎么做到的？首先，创建一个新的**人工控制员**:

```
const abortCtrl = new AbortController(); 
```

一旦有了 **AbortController** 的实例，就可以使用它的 API 来中止获取请求。**中止控制器**包括一个属性**信号**和一个功能**中止**。**信号**属性用于获取一个 **AbortSignal** 对象。获取请求使用该对象在获取请求和 **AbortController** 之间进行通信。您传递 **AbortSignal** 对象来获取函数，作为其 options 对象的一部分。下面是一个使用示例:

```
const abortCtrl = new AbortController();
const signal = abortCtrl.signal;
fetch(url, { signal }).then((response) => {
  ...
}).catch((e) => {
  ... 
}); 
```

一旦获取函数包含一个信号，您就可以中止获取请求。如果你需要中止请求，你所要做的就是调用**中止控制器** **中止**函数。例如:

```
abortCtrl.abort(); 
```

被中止的请求将导致获取承诺被拒绝，并带有 **AbortError** 。这意味着 catch 回调函数将与带有错误代码 20 的 **AbortError** 对象一起运行。使用这些知识，您可以添加逻辑来处理中止场景:

```
const abortCtrl = new AbortController();
const signal = abortCtrl.signal;
fetch(url, { signal }).then((response) => {
   ...
}).catch((e) => {
  if (error.code === 20) { // abort occurred
   // Do something after abort occurred
  }
}); 
```

#### 总结

这篇短文解释了什么是**abort controller**以及如何在你的代码中使用它。如果你觉得有用，请在评论中告诉我。