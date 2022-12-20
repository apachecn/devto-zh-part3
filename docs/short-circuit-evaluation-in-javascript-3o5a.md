# Javascript 中的短路评估

> 原文：<https://dev.to/akhil_001/short-circuit-evaluation-in-javascript-3o5a>

短路是逻辑运算符(&&和||)处理不同类型操作数的一种独特方式。

逻辑运算符通常被认为是布尔运算符。然而，它们通过将不同类型的操作数转换成布尔原语来处理它们。

转换过程以如下方式发生，

1.  表达式的求值从左到右开始
2.  它们会将左侧的值转换为布尔类型。
3.  然后他们决定返回什么，或者 _ 原始左侧值或者 _ 原始右侧值基于操作符。
4.  如果操作符是||，它将返回左侧值(如果它可以转换为 true ),否则它将返回右侧值。例如:

```
var testFunction = function(){
console.log("true");
return true;
}
var testFunction2 = function(){
console.log("false");
return false;
}

var result = testFunction() || testFunction2();
//  prints true in console and never executes the testFunction2()
// result -> true

result = testFunction2() || testFunction();
// prints both false and true in console since the left side expression returns false
// result -> true 
```

在上面的例子中，testFunction2()永远不会被求值，因为

*   如果||运算符在其左侧遇到真值表达式，则它不关心右侧表达式，因此右侧表达式永远不会被求值。
*   如果||运算符在其左侧遇到 falsy 表达式，则它计算右侧表达式并返回后者

行为与&&运算符类似，但反过来。

*   在这里，如果它在左边遇到真值表达式，那么它返回右边的值
*   如果左边的表达式是假的，它返回左边的值，而不关心右边的表达式

```
var testFunction = function(){
console.log("true");
return true;
}
var testFunction2 = function(){
console.log("false");
return false;
}

var result = testFunction() && testFunction2();
// prints true and false in console
// result -> false

result = testFunction2() && testFunction();
//prints false in console and doesnot evaluate testFunction()
//result -> false 
```

还要记住，很少有表达式总是假的(转换为假)

*   空
*   不明确的
*   圆盘烤饼
*   空字符串('，""，``)

如果你还在读这篇文章，那么恭喜你。您已成功到达帖子末尾。但是在离开之前，如果你仔细观察短路的概念，你可以看到与 if-else 条件句的相似之处。我的下一篇[帖子](https://dev.to/akhil_001/replacing-the-if-else-conditionals-with-logical-operators-and-4hek)是关于使用短路回退到默认值和替换 if-else 条件。希望你喜欢这篇文章。请随意发表您的问题和建议

### 有用的链接

*   [关于逻辑运算符的 MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators)

### 学分

*   [来自 unsplash 的 Linh Ha 的照片](https://unsplash.com/photos/ZYdKWY_sOJ8)