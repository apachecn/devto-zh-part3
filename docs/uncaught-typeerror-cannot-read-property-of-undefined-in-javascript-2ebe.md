# 未捕获的类型错误:无法读取 JavaScript 中未定义的属性

> 原文：<https://dev.to/lambdatest/uncaught-typeerror-cannot-read-property-of-undefined-in-javascript-2ebe>

在 JavaScript 中定义的六种基本类型中，即 boolean、string、symbol、number、Null 和 undefined，没有其他类型会抛出 Undefined 那么多的错误。

当脚本遇到未初始化的变量或对象时，通常会遇到这种错误。

### 描述

“Undefined”是全局对象的属性。如果你不给一个变量赋值，它就是“未定义”类型。当被评估的变量没有任何赋值时，代码也返回未定义的值。

### 代码结构

```
function test(t) {      //defining a function
  if (t === undefined) {       //if t=undefined, call tt
        console.log(t.tt)      //call tt member from t
  }
  return t;    
}

var a;    //a is a variable with undefined value

console.log(test(a)); //function call

```

### 错误

当您运行这段代码时，您将得到:

[![](img/224e3edb4785c6bf55e767104d6b76cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n_zsVJIM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/undefined1.png)

### 调试

如果你得到一个未定义的错误，你需要确定哪个变量抛出了未定义的错误，给它赋值。

```
function test(t) {      //defining a function
  if (t === undefined) {       //if t=undefined, call tt
        console.log(t)      //call t
  }
  return t;    
}

var a=10;    //a is a variable with undefined value

console.log(test(a)); //function call

```

在我给 a 赋值后，函数将返回 t 的值，映射为 a=10。

**输出= 10**

防患于未然。

与其他语言如 Java、C 等相比，JavaScript 的优点之一是能够处理未知的变量/对象。很多时候，开发人员都想使用这个特性，尽管这不是一个很好的实践。因此，如果您是一个沉迷于不初始化变量的开发人员，您至少可以尝试通过简单的语句在问题发生之前将其捕获

```
if (typeof(jsvariable) == 'undefined') {
  ...
}

```

希望这能解决你的一点问题。如果你面临任何错误或者有其他选择，请在下面的评论区告诉我们。

原文来源: [LambdaTest 博客](https://www.lambdatest.com/blog/undefined-type-error-in-javascript/)

相关帖子:

1.  [(未知):JavaScript 中的脚本错误](https://www.lambdatest.com/blog/script-error-in-javascript/)
2.  [语法错误:JSON.parse:错误的解析](https://www.lambdatest.com/blog/syntaxerror-json-parse-bad-parsing/)
3.  [排版和跨浏览器兼容性测试](https://www.lambdatest.com/blog/typography-and-cross-browser-compatibility/)