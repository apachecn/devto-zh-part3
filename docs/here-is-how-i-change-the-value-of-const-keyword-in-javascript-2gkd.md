# 下面是我如何在 Javascript 中更改 const 关键字的值

> 原文：<https://dev.to/shearytan/here-is-how-i-change-the-value-of-const-keyword-in-javascript-2gkd>

每个 Javascript 开发者都知道 **var** 和 **let** 是可重赋值的，但是 **const** 不能被重赋值或重声明。

但是有一个关于 **const** 的小秘密，我们来看一些代码。

```
const val = 10; // 10
val = 15; // Uncaught TypeError: Assignment to constant variable 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，我们无法将 *val* 重新分配给另一个号码。弦乐怎么样？

```
const str = 'I am a String'; // 'I am a String'
str = 'I am a Cheese Stringers now'; // Uncaught TypeError: Assignment to constant variable 
```

Enter fullscreen mode Exit fullscreen mode

还是没有，数组和对象呢？

```
// Array
const arrVariable = [10, 11, 12, 13]; // [10, 11, 12, 13]
arrVariable = [14, 15, 16]; // Uncaught TypeError: Assignment to constant variable 
```

Enter fullscreen mode Exit fullscreen mode

```
// Obj
const objVariable = {1: 10, 2: 20, 3: 30, 4: 40}; // {1: 10, 2: 20, 3: 30, 4: 40}
objVariable = {5: 50, 6: 60}; // Uncaught TypeError: Assignment to constant variable 
```

Enter fullscreen mode Exit fullscreen mode

Javascript:不，不，不，你不能这么做...
但是如果我们这样做呢:

```
const arrVariable = [10, 11, 12, 13]; // [10, 11, 12, 13]
arrVariable.push(14); // [10, 11, 12, 13, 14] 
```

Enter fullscreen mode Exit fullscreen mode

什么？！让我们继续玩一会儿...

```
arrVariable[0] = 'Eat'; // ['Eat', 11, 12, 13, 14]
arrVariable[1] = '🥑'; // ['Eat', '🥑', 12, 13, 14]
arrVariable[2] = {1: 'Avocado'}; // ['Eat', '🥑', {1: 'Avocado'}, 13, 14]
arrVariable[3] = true; // ['Eat', '🥑', {1: 'Avocado'}, true, 14] 
```

Enter fullscreen mode Exit fullscreen mode

天哪，刚刚发生了什么？

在 MDN 网络文档中，它描述了:

> const 声明创建对值的只读引用。这并不意味着它保存的值是不可变的，只是说**变量标识符不能被重新赋值**。例如，在内容是对象的情况下，这意味着对象的内容(例如，其属性)可以被改变。

这里的变量标识符/常量是谁？ *arrVariable* ，不是数组本身。

MDN 表示变量标识符/常量不能被重新分配，也就是说*arr variable*T2 不能被重新分配。但是数组呢？没有任何作用，当然还是可变的。

**const** 告诉读者你的变量不能被重新赋值，这也是强烈推荐使用的原因。它防止我们产生一些不必要的错误，并提高代码的可读性。

类似对象:

```
const objVariable = {1: 10, 2: 20, 3: 30, 4: 40}; // {1: 10, 2: 20, 3: 30, 4: 40}
objVariable[1] =  '🍕'; // {1: '🍕', 2: 20, 3: 30, 4: 40}
objVariable[2] = ['Pizza', 'is', 'life']; // {1: '🍕', 2: ['Pizza', 'is', 'life'], 3: 30, 4: 40}
objVariable[3] = true; // {1: '🍕', 2: ['Pizza', 'is', 'life'], 3: true, 4: 40}
objVariable[5] = {1: '🍺', 2: '🍔'} // {1: '🍕', 2: ['Pizza', 'is', 'life'], 3: true, 4: 40, 5: {1: '🍺', 2: '🍔'} 
```

Enter fullscreen mode Exit fullscreen mode

所以下次如果有人问你关于我们的朋友 **const** 的事，你知道该怎么说。

最后，

```
arrVariable = 'I am an 🥑'; // Uncaught TypeError: Assignment to constant variable 

😑😑😑😑😑 
```

Enter fullscreen mode Exit fullscreen mode

不管怎样，还是没有...