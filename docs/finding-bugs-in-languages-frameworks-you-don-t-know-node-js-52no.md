# 在你不知道的语言/框架中寻找 bug:node . js

> 原文：<https://dev.to/therealdarkmage/finding-bugs-in-languages-frameworks-you-don-t-know-node-js-52no>

我正在帮助我的第一个 node.js 学生。他正在用 node.js 编写一个服务器，我帮他找到了丢失的括号、花括号和分号。

我不知道 node.js，但我知道足够多的 Javascript 来帮助我。

我现在有资格发现 node.js 项目中的问题吗？

我能把这个写进我的简历吗？

这怎么可能呢？

* * *

为了帮助他们，我在会议期间问了自己一些问题:

1.  如何检查一个对象是否是数组？
2.  如何检查一个对象是不是字典？
3.  如何验证函数回调是否发生？
4.  如何访问字典中的值？
5.  分号在 Javascript 中重要吗？
6.  如何在 Node.js 中处理 POST 请求体？

* * *

### 1。如何检查一个对象是否是数组？

这个很简单。这有一个内置的方法:

```
isArray = Array.isArray(someArray); 
```

* * *

### 2。如何检查一个对象是不是字典？

这个不太直接，但是如果你知道应该在上面的键，你可以试着访问它们。

```
v = someDict["someKnownKey"]; 
```

如果一个已知键存在一个值，那么这个对象就是一个字典。

关于这一点，我应该进一步详细说明...提前知道密钥是不够的，也不总是可能的。

* * *

### 3。如何验证函数回调是否发生？

如果可能的话，我喜欢使用`alert()`,但是其他情况下`console.log()`就足够了。

* * *

### 4。如何访问字典中的值？

```
v = someDict["someKey"]; 
```

* * *

### 5。分号在 Javascript 中重要吗？

最初，我想“不”,但是...

https://stack overflow . com/questions/11978698/do-we-need-a-semiconductor-after-function-declaration？lq=1

事实证明确实如此。

就像在 C 中一样，函数定义不需要它们:

```
function test() {
    // ...
} 
```

但是，如果你给一个变量赋值一个函数:

```
var f = function test() { 
    // ...
}; 
```

那么，这很重要。

* * *

### 6。如何在 Node.js 中处理 POST 请求体？

[https://it next . io/how-to-handle-the-post-request-body-in-node-js-without-using-a-framework-CD 2038 b 93190](https://itnext.io/how-to-handle-the-post-request-body-in-node-js-without-using-a-framework-cd2038b93190)

在文件的顶部，添加这一行:

```
const { parse } = require("querystring"); 
```

然后，您可以像这样解析 POST 主体:

```
parsedData = parse(postData); 
```

返回的对象是一个 JSON 字典，所以可以用同样的方式访问它的值。

* * *

我设法帮助我的学生在 90 分钟内解决我们遇到的每一个 bug，并设法解决他们遇到的所有问题。

有趣的是，我几乎没有使用 node.js 的经验，但我能够挖掘他们遇到的问题，使用简单的缩进过程来排列代码块以识别语法错误，询问一些关于程序预期操作的基本问题，验证关于对象/变量状态的假设，并从以前的错误转移到当前的工作。

这是一个存在于整个技术世界的思维过程，是进步的驱动因素，也是发现和制造事物的驱动因素。就问问题。一旦你使用了这么多的语言和框架，你就会对它们有一个总体的感觉，这扩大了你在大小环境中识别问题的能力。

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)