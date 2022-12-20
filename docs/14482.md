# Function.bind.bind 在 JavaScript 中不起作用

> 原文：<https://dev.to/akashkava/functionbindbind-does-not-work-in-javascript-59am>

令我惊讶的是，我写了代码，

```
function printThis() {
   console.log(this);
}

const f = printThis.bind(5).bind(7);
f(); // prints 5 instead of 7

f.call(9); // still prints 5 
```

Enter fullscreen mode Exit fullscreen mode

当一个函数已经绑定在事件处理程序中时，这就产生了一个问题，如果某个函数这样做并破坏了库，就会导致噩梦。理想情况下，它应该抛出一个错误，或者应该有一种方法来检测函数是否已经被绑定。

那么我怎么知道函数是否已经被绑定了呢？

虽然这是一个很好的面试问题，但我不知道 JS 的一部分。