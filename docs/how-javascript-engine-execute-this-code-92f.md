# JavaScript 引擎如何执行这段代码？

> 原文：<https://dev.to/coderoo7/how-javascript-engine-execute-this-code-92f>

我正在尝试用 JavaScript 写一篇关于执行上下文的文章。但是我无法理解编译代码时执行堆栈创建阶段，以及编译后的代码是如何组织的。例如

```
 console.log(a); //log 'undefined' why?
   logName();

   function logName(){
       console.log(name); //log 'undefined'
   }

   var a = 10;
   var name = "CoderOO7"; 
```

js

根据我的 JavaScript 引擎工作如下:

1.  首先创建全局执行上下文
2.  通过搜索变量和函数声明来定义全局范围。这就是托管概念的由来
3.  设置变量 a =未定义，name =未定义，this = window 对象。
4.  将搜索变量和函数放在内存中以供参考。
5.  当函数 logName()被调用时，创建它的执行上下文，然后设置它的本地作用域和 **this** 的值。

整个画面可以描述为:

```
<global execution context>
    a : undefined
    name : undefined
    logName : <refrence to function in memory>
    ScopeChain : 
    this : <window object>

    <function execution context of logName>
    ScopeChain:
    this : <window object> 
```

现在，当编译阶段完成后，JavaScript 引擎执行代码并将值赋给变量。

现在画面看起来是这样的

```
<global execution context>
    a : 10
    name : 'CoderOO7'
    logName : <refrence to function in memory>
    ScopeChain : 
    this : <window object>

    <function execution context of logName>
    ScopeChain:
    this : <window object> 
```

有人帮我澄清我的困惑，因为我知道我所想的是完全错误的。