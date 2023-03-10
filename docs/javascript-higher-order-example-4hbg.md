# JavaScript 高阶示例

> 原文：<https://dev.to/amirulabu/javascript-higher-order-example-4hbg>

这是摘自[学反应](http://shop.oreilly.com/product/0636920049579.do)

```
 var createScream = function(logger) {
    return function(message) {
        logger(message.toUpperCase() + "!!!")
    }
}
const scream = createScream(message => console.log(message))

scream('functions can be returned from other functions')
scream('createScream returns a function')
scream('scream invokes that returned function') 
```

真正理解这里发生的事情是一项艰巨的任务。一个迫在眉睫的问题是，如何将`scream()`中的字符串一直传递到`logger`然后是`toUpperCase()`然后是`console.log()`？

使用 ES6 粗箭头
更容易理解

```
 const createScream = (logger) => (message) => {logger(message.toUpperCase() + "!!!")}
const scream = createScream(message => console.log(message))
scream('functions can be returned from other functions')
scream('createScream returns a function')
scream('scream invokes that returned function') 
```

如果你现在还不明白，不要担心。我会给你解释清楚的。

这是编写上面代码的另一种方式，但是跳过了高阶函数，取而代之的是我们硬编码的`console.log()`功能。

```
 const scream2 = (message) => {console.log(message.toUpperCase() + "!!!")}

scream2('the string is passed to message') 
```

字符串`'the string is passed to message'`将传递给`scream2()`参数，然后变成大写字母并用感叹号连接起来。这将首先发生，因为它们在一个括号中。那么答案就是`console.log()`出来了。

另一种看待它的方式是把功能分开。

```
 function scream3(message) {
  return message.toUpperCase() + "!!!"
}

function logger(message){
    return console.log(message)
}

logger(scream3("this is another test")) 
```