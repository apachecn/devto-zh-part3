# Array.foreach vs for()😰

> 原文：<https://dev.to/amatosg/arrayforeach-vs-for-4l47>

事实上，我一直以为他们是同一性的，我没想到一个角色和另一个角色可能有这么大的区别。

事实证明，执行“T0”意味着执行只需不超过 96%的时间(！！！)比一个`for`和例如按钮:

```
Benchmark.prototype.setup = function() {
    // Populate the base array
    var arr = [];

    for (var i = 0; i < 1000; i++) {
        arr[i] = i; 
    }

    function someFn(i) {
        return i * 3 * 8;
    }        
}; 
```

Enter fullscreen mode Exit fullscreen mode

执行此操作时，我们有以下内容(您可以在此处试用):

[![alt text](img/5adda0c2c0f2a88e740227d9856e86d9.png "Logo Title Text 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--0XNuVj0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amatos.dev/conteimg/2019/03/image.png)

很明显，当我们处理成千上万条记录时可以看出这一点，但最好从一开始就实施良好做法

有了这种证据，绝对禁止在任何有利于单纯可爱的`for`的发展中使用`foreach`

顺便说一下，这并不适用于 TypeScript，因为‘t0’是编译成‘t1’我们喜欢的