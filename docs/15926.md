# 保持不变

> 原文：<https://dev.to/vjnvisakh/let--const-22ai>

ES6 引入了两个新的替代关键字 **var** ，即 **let** 和 **const** 。

var 允许你一遍又一遍地定义同一个变量，没有任何错误。

```
var x = 10;
var x = 10; // Doesn't throw up an error

console.log(x); 
```

Enter fullscreen mode Exit fullscreen mode

这可能会导致变量值的意外改变。

但是，**让**就不是这样了。如果你试图用 **let** 做同样的事情，它会抛出一个错误。

```
let x = 10;
let x = 10; // Duplicate declaration error.

console.log(x); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，当你想创建一个永远不会改变它的值的变量时，就使用 const 。

```
const x = 10;
x = 12; // Cannot reassign ever.

console.log(x); 
```

Enter fullscreen mode Exit fullscreen mode

**功能和块范围**

当 **var，let 和 const** 有函数级作用域时， **let 和 const** 更进一步。它们也允许块级范围。

```
 var x = 10;
let x1 = 10;

if (1)
{
   var y = 10;
   let y1 = 10;
}

console.log(y); // Y is available over here.
console.log(y1); // Y is not available over here. 
```

Enter fullscreen mode Exit fullscreen mode

### **接下来:**