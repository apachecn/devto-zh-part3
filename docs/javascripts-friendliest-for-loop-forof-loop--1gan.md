# JavaScript 最友好的 for 循环:for...循环的

> 原文：<https://dev.to/runosaduwa/javascripts-friendliest-for-loop-forof-loop--1gan>

[![](img/e3e80a69e2c01c15a15a964e298bee0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bqjto9ag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lspnw6e19x5hg2n8qt7v.jpg) 
多年来，在 JavaScript 中，我们一直使用`for`、`for-in`和`forEach`(在数组的情况下)对**可迭代对象**进行**迭代**。

我无法想象如果没有这些结构，编程会有多难，它们确实挽救了生命，但是 ES6 又出现了，还有另一个强大的、更干净的，在我看来也是最友好的 for 循环:`for...of`循环。

`for...of`循环允许使用更少、更简洁的语法对数组、字符串、映射等可迭代对象进行迭代。

让我们考虑一个常规的`for-loop`来迭代一个数组:

```
 let names = ["runo", "peter", "saduwa"];

for(let i=0; i<names.length; i++){
 console.log(names[i]);
}
// runo
// peter
// saduwa 
```

这种风格绝对没有错，但是使用`for...of`语法我们可以实现同样的事情，而不必初始化计数器变量`i`来跟踪循环。看看我们如何使用`for...of`语法来使我们的代码更加清晰易读:

```
 let names = ["runo", "peter", "saduwa"];

for(let name of names){
 console.log(name);
}
// runo
// peter
// saduwa 
```

看看代码看起来有多干净，`name`变量代表了`names`数组中的每个元素。

**注意:**使用`for...of`循环得到的值必须是可迭代的。iterable 只是一个能够产生迭代器的对象。JavaScript 中可迭代的例子有数组、字符串、映射和集合等。Dom 树中的节点列表。

对字符串的简单迭代:

```
 let name = "Runo";

for(let letter of name){
console.log(letter)
}

// R
// u
// n
// o 
```

## 总结

`for...of`循环是 JavaScript 世界中最新的 for 循环，它将帮助开发人员用更少更干净的代码迭代数组、字符串、映射和集合等可迭代对象。它还通过其集中的语法提高了代码的可读性。

编码快乐！！