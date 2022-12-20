# JS 逗号运算符

> 原文：<https://dev.to/mouseannoying/js-comma-operator-2a77>

一个同事问了我一个关于在 JS 中检查东西的问题。在他的案例中，如果一个对象有一个属性，他需要检查 3 个条件，其中之一是属性值是否与给定值匹配。如果它没有属性，那么他只需要检查两个条件。他不太热衷于将两个条件包含在一个 if 语句中，并问我是否知道如何将它们合并成一个 if 语句，我必须承认我知道有一个 if 语句，但我无论如何也想不出来...

就像一个特别糟糕的运动员的脚一样，它在我身上痒了一夜，然后我开始记起 JS 的一个相当模糊的特征，它似乎是从 C 语言中借来的，即[逗号运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator)。为了检查它是否会做他需要做的事情，我写了这个简单的 [JSFiddle](https://jsfiddle.net/annoyingmouse/gcf12trx/) :

```
let a;

if(true && true && a === 1, a){
  alert("First: a === 1");
}

a = 1;

if(true && true && a === 1, a){
  alert("Second: a === 1");
} 
```

尽管它相当晦涩，所以我有点担心在生产代码中使用它，正如在 [StackOverflow](https://stackoverflow.com/questions/3561043/what-does-a-comma-do-in-javascript-expressions#comment-85813120) 中讨论的。