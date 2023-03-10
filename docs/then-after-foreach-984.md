# 然后在 forEach 之后

> 原文：<https://dev.to/itachiuchiha/then-after-foreach-984>

你好。我需要一个函数来确保 forEach 是在 JavaScript 中完成的。

简单地说，我创建了这个函数；

```
Object.defineProperty(Array.prototype, "asyncForEach", {
    enumerable: false,
    value: function(task){
        return new Promise((resolve, reject) => {
            this.forEach(function(item, index, array){
                task(item, index, array);
                if(Object.is(array.length - 1, index)){
                    resolve({ status: 'finished', count: array.length })
                }
            });        
        })
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我像下面这样使用这个函数；

```
 homePage.disabled = true

orders.asyncForEach(function (order) {
    api.sendOrder(order)
}).then(function (data) {
    homePage.disabled = false
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个函数像我预期的那样工作。

所以朋友们，你们还知道其他方法吗？这是一种捕捉异步操作完成的优化方法吗？