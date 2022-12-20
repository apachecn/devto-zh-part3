# TIL: window.setImmediate()已不存在

> 原文：<https://dev.to/rhysd/til-windowsetimmediate-has-gone-18j2>

`window.setImmediate`是一个非标准的浏览器 API，用于在另一个 JavaScript 上下文中尽快执行给定的函数。
但是 Chrome 去掉了这个。

```
VM237:1 Uncaught ReferenceError: setImmediate is not defined 
```

替代方案是`window.queueMicrotask()`，由 WHATWG 定义为标准规范。

[https://html . spec . whatwg . org/multipage/timers-and-user-prompts . html # microtask-queuing](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#microtask-queuing)

```
window.queueMicrotask(() => console.log('this code runs as soon as possible!')); 
```