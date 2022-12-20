# JavaScript 等式运算符

> 原文：<https://dev.to/flaviocopes/javascript-equality-operators-4b67>

这些运算符接受两个值并返回一个布尔值:

*   检查是否相等
*   `!=`检查不平等
*   检查是否严格相等
*   `!==`检查严格不等式

下面说说我们对于*严*的意思。如果没有严格的检查，在进行比较之前，第二个操作数将被转换为第一个操作数的类型。严格防止这一点。

例子:

```
const a = true

a == true //true
a === true //true

1 == 1 //true
1 == '1' //true
1 === 1 //true
1 === '1' //false 
```

您不能检查对象是否相等:两个对象从不相等。检查可能为真的唯一情况是两个变量引用同一个对象。

需要注意的一些特性:`NaN`总是不同于`NaN`。

```
NaN == NaN //false 
```

`null`和`undefined`值相等，如果在非严格模式下比较:

```
null == undefined //true
null === undefined //false 
```