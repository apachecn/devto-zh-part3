# BuckleScript 的 JavaScript 互操作是如何工作的？

> 原文：<https://dev.to/yawaramin/how-does-bucklescripts-javascript-interop-work-42jg>

BUCKLESCRIPT 因其对 JavaScript interop 的良好支持而成为一个快速的 OCaml-JavaScript transpiler。但是作为一个初学者，你应该如何掌握它的工作原理呢？

要做的第一件事是用书签标记 BuckleScript 文档的 [Interop](https://bucklescript.github.io/docs/en/interop-overview) 部分。第二件要注意的事情是，这些文档并没有涵盖*BuckleScript 互操作使您能够做的所有事情*。要获得更详细的参考资料，我建议对以下内容做书签标记:

*   HTML 格式的原始 BuckleScript 手册。它有点过时了，但是“OCaml 调用 JS”部分涵盖了 docs 站点没有涵盖的一些内容。[https://raw . githubusercontent . com/BuckleScript/BuckleScript/8 a13 b 34 fdba 9 f 0 B4 d 53 f 5433 b 66879 df 167 c 4217/docs/manual . html](https://raw.githubusercontent.com/BuckleScript/bucklescript/8a13b34fdba9f0b4d53f5433b66879df167c4217/docs/Manual.html)
*   glennsl 出色的备忘单概述了大多数互操作代码及其 JavaScript 输出:[https://github.com/glennsl/bucklescript-ffi-cheatsheet](https://github.com/glennsl/bucklescript-ffi-cheatsheet)

最后，一个宝贵的书签工具是“尝试理性”在线游乐场，在这里你可以快速试验互操作代码:[http://reasonml.github.io/en/try.html](http://reasonml.github.io/en/try.html)

## 关键思想

为了更好地理解上面列出的参考资料，对 BuckleScript FFI 代码的作用有一个心理模型是很有帮助的。这里是*的关键思想:* BuckleScript *绑定*(互操作声明)是 BuckleScript 编译器*将*机械翻译成输出 JavaScript 的规范。因此，每个绑定都需要包含足够的信息来将调用点处的 OCaml/Reason 代码*转换成输出 JavaScript。随着时间的推移，您将了解每个绑定中哪些信息应该放在哪里，以生成正确的 JavaScript。这可能有点棘手，但对于实验和观察 JavaScript 输出(如果没有编译错误，它几乎会即时更新)非常有帮助。*

第二个关键思想是:绑定只是一个*声明；*它本身不生成任何输出 JavaScript。输出只在*调用点、*或使用绑定的地方生成。这意味着，例如，你可以发布一个纯 OCaml/Reason 代码的绑定包，生成的 JavaScript 将在其消费包中产生*。*

要记住的第三件事是，BuckleScript 的 interop 非常强大，这意味着通常有多种方法来生成您需要的 JavaScript。有时这是一个关于哪个更正确的观点的问题；有时是考虑语义(您希望输出的 JavaScript 的*意思是*)的问题，有时只是经验问题。

## 举例

这里有一个简单的例子来说明上述想法。我要叫`document.getElementById("main")`。如何着手做那件事？

我将把这个问题分解成几个部分。第一部分是如何得到`document.getElementById`。您可能知道，`document`总是在浏览器的范围内。而`getElementById`总是被`document`对象支撑着。所以，在这种情况下我们可以使用`[@bs.scope]`和`[@bs.val]`扩展:[https://bucklescript . github . io/docs/en/bind-to-global-values # global-modules](https://bucklescript.github.io/docs/en/bind-to-global-values#global-modules)

```
type element;

[@bs.scope "document"] [@bs.val]
external getElementById: string => Js.nullable(element) = "";

/* This triggers the output: */
let main = getElementById("main"); 
```

输出:

```
var main = document.getElementById("main"); 
```

根据输出 JS，机械转换是如何发生的:

| BuckleScript/Reason | 输出 JavaScript |
| --- | --- |
| `let main =` | `var main =` |
| `[@bs.scope "document"]` | `document.` |
| `[@bs.val] external getElementById` | `getElementById(` |
| `"main"` | `"main"` |

这可以工作，但是有点不正确，因为从语义上来说，`[@bs.scope]`应该用于 JavaScript *模块*，而`[@bs.val]`应该用于模块中的*全局值*。

## 举例，方法二

一个更好的方法是将`document`建模为一个全局可用的*对象*，带有一个*抽象类型*和一个相应的方法`getElementById`，根据 Web API 的[，它们在语义上是什么。](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) 

```
type document;
type element;

[@bs.val] external document: document = "";
[@bs.send.pipe: document] external getElementById: string => Js.nullable(element) = "";

/* This triggers the output: */
let main = getElementById("main", document);
/* Or: let main = document |> getElementById("main"); */ 
```

输出:

```
var main = document.getElementById("main"); 
```

这是完全相同的输出，但现在我在原因代码库中对类型和值进行了不同的建模。我显式地声明了一个接受方法调用`getElementById`的`document`类型。这是这种方法的机械翻译，也是从 JavaScript 的角度:

| BuckleScript/Reason | 输出 JavaScript |
| --- | --- |
| `let main =` | `var main =` |
| `[@bs.send.pipe: document]`、`[@bs.val] external document: document = "";`、`getElementById(..., document)` | `document.` |
| `external getElementById` | `getElementById(` |
| `"main"` | `"main"` |

这里我使用了`[@bs.send.pipe: document]`扩展来声明`document`类型支持一个名为`getElementById`的方法并带有一个`string`参数。BuckleScript 在 OCaml/Reason 端将此解释为一个可以用`string`和`document`调用的函数，但会生成输出 JS，该输出 JS 用一个`string`参数调用`document`上的适当方法。

## 结论

BuckleScript 通过使用绑定将*惯用的 OCaml* 输入(模块、类型、函数和值)转换成*简化的 JavaScript* 输出(值、函数、方法调用等)。).这些绑定是以机械的方式捕获进行转换所需的所有信息的声明。通过尝试不同的绑定并观察最终的 JavaScript 输出，您可以获得很多关于如何编写符合习惯且语义正确的绑定的直觉。