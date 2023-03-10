# 理解:Paw Patrol 解释的 JavaScript 中的上下文、作用域、执行上下文和 8 个不同的 This 值！

> 原文：<https://dev.to/carlillo/understanding-context-scope-execution-context-and-8-different-this-value-in-javascript-explained-by-paw-patrol-2gme>

在向职业培训和大学生解释前端 6 年多之后，我发现很难理解 JavaScript 中保留字“this”的用法。这个保留字的问题随着 TypeScript 和 Angular 等框架的出现而被隐藏起来，尽管在这些层下仍然有 JavaScript，缺乏知识会导致需要花费数小时来修复的错误。

[![](img/2a636ff88f46ec0ad13b5d9a3e701e18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xQILv2uO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AXwhIrzmQ1mBiyRug7JwOMg.jpeg)

### 上下文 Vs 范围

我们必须澄清的第一个概念是*上下文*和*范围*之间的区别。这两个术语被许多前端开发人员混淆了(我自己花了一段时间才理解它们)。

所有的功能都有相关的范围和上下文。作用域定义了调用函数时对函数变量的访问。另一方面，上下文总是保留字`this`的值，它是对拥有代码执行的对象的引用。

### 执行上下文

JavaScript 是单线程语言，所以它只能同时执行一个任务。其余的任务在执行上下文中排队。不幸的是，当他们说“执行上下文”时，他们指的是范围(他们为什么这样做？).

在每次调用中，函数都会将其上下文附加到执行上下文中。所以每个函数都创建自己的执行上下文(自己的作用域)。

一旦调用结束，上下文就被销毁，执行上下文将被转移到父上下文。只有一个全局上下文，但有有限个函数上下文。

### “此”指全局对象

默认情况下，执行的执行上下文是全局的，这意味着如果代码作为简单函数调用的一部分被执行，那么“this”指的是全局对象。在浏览器中运行代码时，全局对象是“window”对象，而在 node.js 中，全局对象可以是特殊的“global”或“module.exports”。

以下代码正在浏览器中运行。

[![](img/f4d3d42b11ef63281fd1249fd1d6a146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--devybILE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AxeZy0jSK9Orzzo3d1sGd1g.png)

以下代码在 node.js 环境中运行。

[![](img/efc88e45ef06632d142a0dbc1d97552e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4v8qFiN8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Ad6CgHVsAII27zKAL_MlEYw.png)

### “此”指新的实例

当使用“new”关键字调用一个函数时，该函数被称为构造函数，并返回一个新实例。在这种情况下,“this”的值指的是新创建的实例。

**new** 关键字执行以下四项任务:

1.  它创建新的空对象，例如 obj = { }；
2.  它将新的空对象的不可见的“原型”属性设置为构造函数的可见且可访问的“原型”属性。(每个函数都有可见的“原型”属性，而每个对象都包含不可见的“原型”属性)
3.  它将使用`this`关键字声明的属性或函数绑定到新对象。
4.  它返回一个已创建的对象，除非构造函数返回一个非原始值(自定义 JavaScript 对象)。如果构造函数不包含 return 语句，编译器将插入“return this”隐式地放在函数的末尾。如果构造函数返回一个原始值，那么`return this;`将不会被插入。

[![](img/6be77bcc1e7ee9f9eb03c6f8d1d12918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bjimWonH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AhXEtAkeOKBxLZSPicBz9qA.png)

现在我们有了一个新的执行上下文，可以为 Dog 函数定义新的属性，在这种情况下，我们得到了 Paw Patrol、Turbot 和 crestle 这两条新的狗。

[![](img/e390e1a2dcd7fd71f18880675994ee7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0jDcUZhO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AKOB5DaXcA8z4CnfRDfZZ7g.png)

正如你可能知道的，Javascript 的最大优势是使用每个函数的原型(我推荐你去阅读其他面向对象语言中使用的原型模式，因为它们不是本地的)。

[![](img/a52a7ac6cc6bcf7f56b05ec99d79e100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7D7iycDP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A6tzarcueG0omnAoCRyTBcA.png)

### “本”指调用者对象(父对象)

在 JavaScript 中，对象的属性可以是函数或简单的值。当一个对象的方法被调用时,“this”指的是包含被调用方法的对象。

在下面的示例中，您可以看到该值如何根据执行上下文而不同。

[![](img/5d3400f5583cdc6d19034c7299749744.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8SvD3cwo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A72E35PPIrj4Wy_tFffI6Tg.png)

### “本”用调用或应用方法

JavaScript 中的函数也是一种特殊类型的对象。每个函数都有`call`、`bind`和`apply`方法。这些方法可用于将自定义值“this”设置为函数的执行上下文。

在下面的代码中，您可以看到如何使用 call 方法更改执行上下文。

[![](img/d7c14a45fe7abb99d35c3c41f021f708.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fceHW-hD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AD0B-N2Se5YmJggwRFOD3eA.png)

### 【本】同 bind 法

`bind`方法返回一个新方法，其中“this”是指传递的第一个参数。

[![](img/31b3ed458db563a7697965c3a2a474f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N-HfDVvi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AeIcoelINumD3YPJcf6Fr1g.png)

### 【本】具有胖箭头功能

作为 ES6 的一部分，引入了一种定义函数的新方法；使用`fat arrow` (= >)。

[![](img/fef853a1bce8312c29e35b62fed29b42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X2yKHmmO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AOCsHabQyBcCt_MjZdzwSrA.png)

当使用粗箭头时，它不会为“this”创建新值。“this”在函数之外继续引用它所引用的同一个对象。没有新的执行上下文。

下面的代码是函数`growUp`的值，它的执行上下文中没有属性`age`。

[![](img/d1e0cff4e80fcafd003d0d2df711ac2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e8Fbw61f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AgJ2uB_ZJly0KiGYiRhQNnA.png)

在这种情况下，解决方案不是创建一个新的执行上下文。因此，我们可以使用粗箭头。

[![](img/4f0556143cf05ae06354ac3bb87cdf1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAnHlNmT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A9gERpCciMe3wZzOFDS1Ijg.png)

### 【本】带有胖箭头功能和应用-调用

当你在胖箭头函数上使用 apply 或 call 方法时，由于胖箭头不拥有`this`，所以*这个*没有改变。因此，apply 和 call 方法只调用带参数的原始方法，而忽略“thisArg”。

[![](img/9967f712ec33590b8f635b0cf43eef76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x0FJwKgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Atho-lIeFZeoFD5uiZH4XqA.png)

### 【本】同类糖语法

当我们使用`class` sugar 语法时，通常会像使用任何其他面向对象编程语言一样使用`this`。然而，大多数 OOP 语言不允许在函数中定义函数。

因此，如果我们看一下下面的代码，有一个方法
`displayName`包含一个名为`innerDisplay`的方法，该方法使用了
关键字`this`。如果我们在这个上下文中执行`innerDisplay`函数，我们将创建一个新的执行上下文，因此`this`值将不属于`Dog`类。然而，为了解决这个问题，我们可以使用本博客中解释的任何技巧。在这种情况下，我们将使用`apply`函数将`innerDisplay`函数的上下文更改为`Dog`的上下文。

[![](img/154675572fb5b95601ea41d983073df4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h9NhiQ0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AdWft80OhVTjMo_JLNbWBUg.png)

### 更，更，更…

*   [https://www . tutorialsteacher . com/JavaScript/new-keyword-in-JavaScript](https://www.tutorialsteacher.com/javascript/new-keyword-in-javascript)
*   [https://medium . com/quick-code/understanding-the-this-keyword-in-JavaScript-CB 76d 4 c 7 C5 e 8](https://medium.com/quick-code/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8)
*   [https://JavaScript sexy . com/understand-JavaScript s-this-with-clarity-and-master-it/](https://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)
*   [http://ryanmorr . com/understanding-scope-and-context-in-JavaScript/](http://ryanmorr.com/understanding-scope-and-context-in-javascript/)
*   [https://medium . com/@ marjanrab/JavaScript-scope-context-and-this-under-the-hood-43c 32033 c9f 9](https://medium.com/@marjanrab/javascript-scope-context-and-this-under-the-hood-43c32033c9f9)
*   [https://scotch . io/tutorials/understanding-scope-in-JavaScript](https://scotch.io/tutorials/understanding-scope-in-javascript)
*   [https://medium . com/JavaScript-in-plain-English/hello-JavaScript-this-bb 97 c 54 f 0823](https://medium.com/javascript-in-plain-english/hello-javascript-this-bb97c54f0823)

* * *

*最初发布于[https://www . carloscaballero . io](https://www.carloscaballero.io/understanding-context-scope-execution-context-and-8-different-this-value-in-javascript-explained-by-paw-patrol/)2019 年 5 月 17 日。