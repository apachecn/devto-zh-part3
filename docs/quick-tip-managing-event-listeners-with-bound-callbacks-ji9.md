# 快速提示:使用绑定回调管理事件侦听器

> 原文：<https://dev.to/tevko/quick-tip-managing-event-listeners-with-bound-callbacks-ji9>

## 始终删除您的事件监听器

使用完事件侦听器后，一定要记住删除它们。这有利于提高性能，并允许对代码进行垃圾收集，并在不再需要时将其从内存中删除。

### 问题

考虑一些类似
的代码

`thing.addEventListener('click', this.func.bind(this))`

不幸的是，您不能以同样的方式删除事件侦听器。即:

`thing.removeEventListener('click', this.func.bind(this))`

根本行不通。**悲伤的 trombbone **

## 这个为什么不行？

这是行不通的，因为每次使用 bind，[都会创建一个新函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)！

这意味着当需要调用`removeEventListener`时，回调函数不再匹配在`addEventListener`中使用的原始函数(匿名函数也会这样)。

## 修罗

```
const func = doStuff.bind(this);
thing.addEventListener(func);

/** later on **/

thing.removeEventListener(func); 
```

Enter fullscreen mode Exit fullscreen mode