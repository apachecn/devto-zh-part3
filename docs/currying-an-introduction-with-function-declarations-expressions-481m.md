# Currying -函数声明和表达式简介

> 原文：<https://dev.to/stephencweiss/currying-an-introduction-with-function-declarations-expressions-481m>

很长一段时间，我很讨厌看到这样的函数:`const someFn = a => b => a + b;`。我认为这只是“代码高尔夫”(将一个函数缩减到它的最短的 T2 化身的想法)，而不考虑它将如何被代码的读者 T4 接收。

这肯定是真的，我仍然普遍反对高尔夫运动本身。但是，我没有想到的是，以这种方式编写函数，也就是说，使用 currying，实际上是非常有用的。

通过利用函数的可组合性，Currying 可以更好地控制函数正在做什么(通过缩小每个函数的范围)。

让我们从一个函数声明加法的例子开始。稍后，我们将进入 ES6 和函数表达式。

这个函数的目的很简单，但正是这个例子帮助我了解了*如何奉承！* 

```
function addOne(a) {
  return a + 1
}

function addNums(a, b) {
  return a + b
}

function addNumsCurried(a) {
  return function addBy(b) {
    return a + b
  }
} 
```

如果我们知道我们总是想加 1，`addOne`是完全合理的。如果我们总是设置两个变量，我们可以使用`addBy`。`addByCurried`看似根本不同，但它实际上允许我们通过*独立于*我们的基地来决定我们想要添加什么。

所以，我们可以有下面的

```
const addByTwo = addNumsCurried(2)
const addByThree = addNumsCurried(3)
console.log(
  `The typeof addByTwo and addByThree --> `,
  typeof addByTwo,
  typeof addByThree
) // The typeof addByTwo and addByThree --> function function 
```

需要注意的是，在赋值点，`addByTwo`和`addByThree`是*函数*。

这很好，因为这意味着我们可以调用它们！我们可以通过回到我们的控制台并测试它来看到这一点:

```
console.log(addByTwo) //
// ƒ addBy(b) {
// return a + b;
// } 
```

具体来说，它们是接受单个参数的函数`addBy`。

```
addByTwo(3) // 5
addByThree(3) // 6 
```

好了，现在让我们过渡到函数表达式和 ES6(为了便于比较，我假设我们在一个全新的全局范围内，所以我们不会有任何名称冲突问题或之前分配的`const`变量):

```
const addOne = a => a + 1
const addNums = (a, b) => a + b
const addNumsCurried = a => b => a + b 
```

等等，什么？

`AddNumsCurried`利用箭头函数提供的两个语法糖特性:

1.  如果只有一个参数，括号(`()`)是可选的
2.  如果 return 语句只有一行，那么有一个隐式的 return，大括号(`{}`)是不必要的

这意味着`addNumsCurried`也可以写成:

```
const addNumsCurriedAlt = (a) => {
  return (b) => {
    return { a + b }
  }
} 
```

这看起来非常类似于函数声明。这才是重点！

如果我们更进一步，将我们新的加法技巧用于数组元素，会怎么样？

```
const addOneToEachBasic = ar => ar.map(num => num + 1)
const addOneToEachCompartmentalized = ar => ar.map(num => addOne(num))
const addOneCurried = ar => ar.map(addOne) 
```

个人认为，`addOneToEachComparatmentalized`和`addOneCurried`的区别就是当灯泡当关！我遇到了这个问题一个*吨*与`.reduce`在那里我想分开我的减速器和定义它分开，但我总是遇到麻烦！

直到我看到这两个并排产生相同的结果，我才更好地理解了*正在发生什么。*

让我们考虑一下:我们的数组充满了数字，但它们可以表示为字符串*或*数字(但总是一个或另一个)。为了检查，我们可以使用三元组来检查类型。我们将把匿名函数赋给变量`ensureNum`。

```
// add type checking to make sure everything is a number
const ensureNum = val => (typeof val == 'string' ? Number(val) : val) 
```

我们希望在之前添加

```
const addOneToEachWithType = ar => ar.map(ensureNum).map(num => num + 1)

const addOneToEachWithTypeAndCurry = ar => ar.map(ensureNum).map(addOne) 
```

最后一步:让我们现在说，我们想不只是加 1，但任何数字。我们可以使用函数声明中相同的 currying 技术，以下面的方式编写函数表达式。

```
const addByToEachWithType = (ar, by) =>
  ar.map(ensureNum).map(addNumsCurried(by)) 
```

感谢 Jacob Blakely 和他对[阿谀奉承](http://codekirei.com/posts/currying-with-arrow-functions/)的精彩描述——这既是这次练习的灵感来源，也是我的指南。