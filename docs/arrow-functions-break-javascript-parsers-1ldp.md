# 箭头函数破坏 JavaScript 解析器

> 原文：<https://dev.to/samthor/arrow-functions-break-javascript-parsers-1ldp>

这是一个非常深奥的帖子！享受吧。🔨🤓

在用 C 语言编写 JavaScript 解析器的过程中——这是另一天的事了，但是你可以在这里通过 Web Assembly online 试用一下[——我发现了 JavaScript 唯一真正的模糊之处。](https://bit.ly/2UATDIM)

惊喜！这是箭头函数，你最喜欢的书写方法和回调的简写。快速提醒一下它的样子:

```
const method = (arg1, arg2) => {
  console.info('do something', arg1, arg2);
};
const asyncMethodAddNumber = async foo => (await foo) + 123; 
```

Enter fullscreen mode Exit fullscreen mode

## 就为了使用一个网站来解释一个 1231 块的样子？

箭头函数在声明它们的作用域中使用`this`变量。这里有一个经典的例子:

```
class Foo {
  constructor(things) {
    this.total = 0;
    things.forEach((thing) => this.total += thing);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您将上面的代码改为使用`function (thing) { ... }`，访问`this.total`将会失败:`this`将不会被正确设置。而且一般来说，我的建议是用*默认*来写`() => ...`。我相信它有[最没有惊喜的](https://www.google.com/search?q=least+surprise)。

<small>(这里是[另一篇关于 JS 中理智默认的](https://dev.to/samthor/declaring-js-variables-in-2019-4572)帖子，围绕`let`、`const`和`var`！)</small>

## 暧昧

所以:在编写解析器时，你的目标是找出每个标记是什么，以及它组成哪种语句或表达式。JavaScript 的语法使得这个*变得相当简单，大多数情况下需要你查看最多一个“forward”标记。*

### 容易:放手

这里有一个关于`let`的例子。你知道吗— `let`只是*有时候*是一个关键字(用来定义新变量)，有时候是一个有效的变量名本身？^

```
let += 123;  // let is a symbol which I'm adding 123 to
let abc;     // I'm declaring a variable "abc" 
```

Enter fullscreen mode Exit fullscreen mode

<small>(注意这里 dev.to 的语法 highlighter 是错的！🤣)</small>

所以`let`是一个关键字，如果:

*   你在执行的顶层(不在括号内)
    *   ...除非您在“for”声明中，例如:`for (let ...`
*   下一个令牌是一个文字(或者`[`和`{`，对于`let {x,y} = ...`
*   下一个令牌是*而不是* `in`或`instanceof`
    *   ...如`let in foo`所问，“let”中包含的变量是对象“foo”的一个键吗

### 硬:箭头功能

但是这个帖子讲的是卑微的箭头功能！现在，一个箭头函数的开始可以有两种不同的形式。第一种更简单，可以作为箭头函数来确定:

```
foo => bar;
async foo => something + await blah; 
```

Enter fullscreen mode Exit fullscreen mode

当解析器遇到`foo`(或任何命名变量)时，我们可以查看下一个标记，并询问它是否是一个箭头`=>`。我们同样可以从`async`开始向前看，因为对`async variableName`唯一有效的解释是异步箭头函数的开始。万岁！🎊

但是在括号的情况下，像这个`(foo, bar)`，我们的解析器*无法*知道该怎么做。这可能只是一个表达式列表:考虑将一些数学放入括号中，以确保正确的求值顺序。

带前缀`async`的箭头函数*更加*模糊:因为`async`在技术上可以是方法调用的名字。是的，没错，下面的 JavaScript 是有效的:🤮

```
var y = 123;
var async = (x) => x * 2;  // assign 'async' to a function
console.info(async(y));    // call 'async' as a function! 
```

Enter fullscreen mode Exit fullscreen mode

我等你复制粘贴到控制台。📥

<small>(还是那句话，语法 highlighter 错了，说`async`是关键字！😂)</small>

## 解

有几个解决方案。无论如何，我们都要向前看，*超过了*这个暧昧的点。重要的是要记住，这可能不是“快”。

这里有一个人为的例子:

```
(arg=function() {
  // whole other program could exist here
}) => ... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想知道第一个`(`是否打开了一个箭头函数，我们可以向前解析来找到后面的`=>`。天真的是，我们将丢弃所有的工作，并再次从`(`开始解析。

但是，如果我们的目标是速度，我们已经扔掉了所有的“工作”。

相反，一个更好的解决方案是故意让它变得不明确，以后再来讨论它。第*种方式*我们解析括号内的内容——幸运的是！–不会因是否是箭头功能而改变。同样的记号，等号等等，在那里都是允许的。

所以我们最终会得到这样一串令牌:

```
AMBIG_PAREN
PAREN       (
SYMBOL      que
OP          =
FUNC        function
...
CLOSE       )
ARROW       => 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以阐明我们的‘AMBIG _ PAREN’—它开始了一个箭头函数声明。这种情况在程序的每个“深度”最多只发生一次:同样的*模糊性可能发生在整个其他程序中，但它会在更深的深度发生。*

## 有些前后关系

^
公平地说，当运行在*严格模式*下时，JavaScript 的一些模糊性得到了解决。

例如，在这种模式下，我们不能使用`let`作为变量名。但是并不是所有的代码都是以这种方式编写或服务的——而且严格模式不会改变`async`或箭头函数模糊性的行为。

### 正斜杠

JavaScript 解析器中还有另一个有趣的挑战:简单的斜线是除法还是正则表达式的开始。例如:

```
function foo() {} / 123 /g 
```

Enter fullscreen mode Exit fullscreen mode

问:虽然上面的代码*是无意义的*,我们不得不问:“除以 123，除以 g”被解析成什么？

答:原来——这是一个正则表达式。这是因为顶级函数是声明，而不是表达式。如果我们用`()`把整条线围起来，那就是除法。

然而，与箭头函数不同，这对于 JavaScript 解析器来说并不是问题。当从左到右遍历代码时，我们可以跟踪我们期望的任何即将到来的斜杠是什么。所以不暧昧。🤷

## 鳍

我告诉过你这很深奥！谢谢你读到这里。就我个人而言，我希望看到 JavaScript 随着它的发展摆脱它的模糊性，但是我认为它的广泛采用将会阻碍修正它的语法中可能只是有点令人讨厌的特质。😄

three