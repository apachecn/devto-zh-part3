# 函数式编程基础第 3 部分:Currying

> 原文：<https://dev.to/ysael/functional-programming-basics-part-3-currying-3bpn>

### 那么这个人们称之为阿谀奉承的东西是什么呢？

Currying 是将一个接受多个参数的函数转换成一个能够支持返回新函数的函数的动作，如果它得到的参数不是最后一个的话。

如果是最后一个参数，它将执行。

假设我有一个简单的函数:

```
const add = (x, y) => x + y; 
```

Enter fullscreen mode Exit fullscreen mode

如果我试图只用一个参数调用它，它会失败...

```
const add = (x, y) => x + y;

add(1) // undefined 
// (x is now 1 but y is undefined, so 1 + undefined = undefined) 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，现代 JavaScript 让我们能够通过箭头函数轻松地进行搜索。

下面是相同的函数:

```
const add = x => y => x + y;

add(1) // function add() 
/* (this newly returned function as encapsulated 
the value of 1 and will look like this --> y => 1 + y ) */ 
```

Enter fullscreen mode Exit fullscreen mode

好的一面是，现在我们可以只用一个参数调用它，这个参数将返回一个新函数，这个函数将期待最后一个参数`y`。

我们也可以把新返回的函数放在一个变量中，然后开始使用它:

```
const add = x => y => x + y;

const add1 = add(1);

console.log(add1(1)) // 2 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助你理解什么是奉承:)