# 通过从头构建 UI 框架学习 JavaScript

> 原文：<https://dev.to/carlmungazi/learning-javascript-by-building-a-ui-framework-from-scratch-1767>

在我之前的文章中，我解释了如何将你最喜欢的库和框架中的 API 转化为编程课程。今天，我将进一步发展这个想法。我们不会去读别人的代码。相反，我们将自己编写并构建一个非常基本的 UI 框架。

构建框架是加深您的 JavaScript 和编程知识的好方法，因为它迫使您探索语言特性和软件工程原则。例如，所有的 web 框架都试图解决保持应用程序数据与 UI 同步的问题。这个问题的所有解决方案可以触及不同的领域，如路由、DOM 操作、状态管理和异步编程。

解决这个 UI 状态同步问题的一个更流行的方法是使用一个被称为**虚拟 DOM** (或 vdom)的概念。我们可以使用 JavaScript 对象，而不是直接操纵 DOM 来响应数据变化，因为操纵它们在计算上要便宜得多。vdom 方法可以细分如下:

1.  当您的应用程序第一次加载时，创建一个描述您的 UI 的 JavaScript 对象树
2.  使用 DOM APIs 将这些对象转换成 DOM 元素，例如`document.createElement`
3.  当您需要对 DOM 进行更改时(响应用户交互、动画或网络请求)，创建另一个 JavaScript 对象树来描述您的新 UI
4.  比较旧的和新的 JavaScript 对象树，看看哪些 DOM 元素发生了变化以及如何变化的
5.  仅在已经更改的地方对 DOM 进行更改

任何 vdom 实现的基本部分之一是创建对象的函数。本质上，这个函数必须返回一个包含创建 DOM 元素所需信息的对象。例如，为了创建这个 DOM 结构:

```
<ul class="list">
    <li class="list-item" style="color: red;">Item 1</li>
    <li class="list-item" style="color: blue;">Item 2</li>
</ul> 
```

您需要了解每个 DOM 元素的以下信息:

*   元素类型
*   属性列表
*   如果它有孩子(对于每个孩子，我们还需要知道上面列出的相同信息)

这就引出了我们的第一课:**数据结构**。正如 Linus Torvalds 所说，“糟糕的程序员担心代码。优秀的程序员担心数据结构和它们之间的关系”。那么如何用代码表示上面的 DOM 结构呢？

```
{
  type: 'ul',
  attrs: {
      'class': 'list'
  },
  children: [
    {
      type: 'li',
      attrs: {
        class: 'list-item',
        style: {
          color: 'red'
        }
      },
    },
    {
      type: 'li',
      attrs: {
        class: 'list-item',
        style: {
          color: 'blue'
        }
      },
    } 
  ]
} 
```

我们有一个具有三个属性的对象，每个属性要么是一个`string`、`object`要么是`array`。我们是如何选择这些数据类型的？

*   所有的 HTML 元素都可以用一个字符串来表示
*   HTML 属性有一种`key: value`关系，这种关系非常适合对象
*   HTML 子节点可以以列表格式出现，创建它们需要对列表中的每个项目执行相同的操作。数组非常适合这种情况

现在我们知道了我们的数据结构是什么样子，我们可以继续学习创建这个对象的函数。根据我们的输出判断，最简单的做法是创建一个带有三个参数的函数。

```
createElement (type, attrs, children) {
  return {
    type: type,
    attrs: attrs,
    children: children
  }
} 
```

我们有自己的函数，但是如果调用时没有收到所有的参数，会发生什么呢？此外，创建我们的对象是否需要每个参数都存在？

这就引出了下一课:**错误处理**、**默认参数**、**析构**和**属性缺省**。

首先，你不能创建一个没有指定类型的 HTML 元素，所以我们需要防止这一点。对于错误，我们可以借用 Mithril 的方法[抛出](https://github.com/MithrilJS/mithril.js/blob/next/render/hyperscript.js#L87)一个错误。或者，我们可以定义自定义错误，如这里描述的。

```
createElement (type, attrs, children) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  return {
    type: type,
    attrs: attrs,
    children: children
  }
} 
```

稍后我们将再次讨论这个检查`type == null || typeof type !== 'string'`,但是现在，让我们专注于创建我们的对象。虽然我们不能在不指定类型的情况下创建 HTML 元素，但是我们可以创建没有子元素或属性的 HTML 元素。

在 JavaScript 中，如果在没有提供任何必需参数的情况下调用一个函数，默认情况下这些参数被赋予值`undefined`。所以如果用户没有指定，`attrs`和`children`将会是`undefined`。我们不希望这样，因为正如我们将在后面看到的，我们代码的其余部分期望这些参数包含一个值。为了解决这个问题，我们将指定`attrs`和`children`默认值:

```
createElement (type, attrs = {}, children = []) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  return {
    type: type
    attrs: attr,
    children: children
  }
} 
```

如前所述，HTML 元素可以在没有任何子元素或属性的情况下创建，因此我们的函数中不需要三个参数，而是需要两个:

```
createElement (type, opts) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  return {
    type: type
    attrs: opts.attr,
    children: opts.children
  }
} 
```

我们已经丢失了先前引入的默认参数，但是我们可以通过析构将它们恢复。析构允许我们解包对象属性(或数组值)并将它们作为不同的变量使用。我们可以将这与速记属性结合起来，使我们的代码不那么冗长。

```
createElement (type, { attrs = {}, children = [] }) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  return {
    type,
    attrs,
    children
  }
} 
```

我们的函数可以创建虚拟 dom 对象，但是我们还没有完成。之前我们跳过了这段代码`type == null || typeof type !== 'string'`。我们现在可以重温一下，学习一些别的东西:**强制**。

这里有四点需要注意:

*   `==`松散相等运算符的行为
*   `||`操作员的行为
*   `typeof`操作员的行为
*   `!==`操作员的行为

当我第一次学习 JavaScript 时，我看到了许多反对使用松散的等式操作符的文章。这是因为它会产生令人惊讶的结果，例如:

```
1 == '1' // true
null == undefined // true 
```

这是令人惊讶的，因为在上面的例子中，我们正在比较四种不同的原始类型的值:`number`、`string`、`null`和`undefined`。上面的检查评估为`true`，因为`==`在比较不同类型的值时执行强制。控制这种情况如何发生的规则可以在[这里](http://www.ecma-international.org/ecma-262/#sec-abstract-equality-comparison)找到。对于我们的具体情况，我们需要知道规范声明`null == undefined`将总是返回 true。同样，`!==`的工作原理是执行与`===`相同的检查，然后否定结果。你可以在这里阅读关于那个[的规则。](http://www.ecma-international.org/ecma-262/#sec-strict-equality-comparison)

回到我们的函数，这个`type == null || typeof type !== 'string'`要检查的第一件事是是否传递了一个`null`或`undefined`值。如果是`true`，`||`操作员将返回`typeof type !== 'string'`的结果。发生的顺序很重要。`||`操作符不返回`boolean`值。它返回两个表达式之一的值。它首先在`type == null`上执行`boolean`测试，该测试将是`true`或`false`。如果测试返回`true`，我们的错误将被抛出。

然而，如果返回了`false`，那么`||`将返回第二个表达式的值，在我们的例子中，它将是`true`或者`false`。如果我们的检查是`type == null || type`，第一个表达式的结果是`false`，那么第二个表达式将返回变量`type`中的任何值。`typeof`操作符返回一个表示给定值类型的字符串。我们没有在我们的`type == null`检查中使用它，因为`typeof null`返回`object`，这是 JavaScript 中一个[臭名昭著的](http://2ality.com/2013/10/typeof-null.html)错误。

有了这些新发现的知识，我们可以更仔细地研究`createElement`，并问自己以下问题:

*   我们如何检查第二个参数是否可以被析构？
*   我们如何检查第二个参数是一个对象？

让我们从调用不同参数类型的函数开始:

```
createElement (type, { attrs = {}, children = [] }) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  return {
    type,
    attrs,
    children
  }
}

createElement('div', []); // { type: "div", attrs: {…}, children: Array(0) }
createElement('div', function(){}); // { type: "div", attrs: {…}, children: Array(0) }
createElement('div', false); // { type: "div", attrs: {…}, children: Array(0) }
createElement('div', new Date()); // { type: "div", attrs: {…}, children: Array(0) }
createElement('div', 4); // { type: "div", attrs: {…}, children: Array(0) }

createElement('div', null); // Uncaught TypeError: Cannot destructure property `attrs` of 'undefined' or 'null'
createElement('div', undefined); // Uncaught TypeError: Cannot destructure property `attrs` of 'undefined' or 'null' 
```

现在我们修改函数:

```
createElement (type, opts) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  if (arguments[1] !== undefined && Object.prototype.toString.call(opts) !== '[object Object]') { 
    throw Error('The options argument must be an object'); 
  }

  const { attrs = {}, children = [] } = opts || {};

  return {
    type,
    attrs,
    children
  }
}

createElement('div', []); // Uncaught Error: The options argument must be an object
createElement('div', function(){}); // Uncaught Error: The options argument must be an object
createElement('div', false); // Uncaught Error: The options argument must be an object
createElement('div', new Date()); // Uncaught Error: The options argument must be an object
createElement('div', 4); // Uncaught Error: The options argument must be an object

createElement('div', null); // Uncaught Error: The options argument must be an object
createElement('div', undefined); // Uncaught Error: The options argument must be an object 
```

我们的第一个函数不符合目的，因为它接受了错误类型的值。当用`null`或`undefined`调用时，它也给了我们一个`TypeError`。我们在第二个函数中通过引入新的检查和新的课程修复了这个问题:**错误类型**、**其余参数**和 **this** 。

当我们使用`null`或`undefined`作为第二个参数调用函数时，我们看到了这条消息:`Uncaught TypeError: Cannot destructure property 'attrs' of 'undefined' or 'null'`。`TypeError`是一个对象，表示由非预期类型的值引起的错误。这是与`ReferenceError`和`SyntaxError`一起更常见的错误类型之一。这就是为什么我们恢复使用一个对象作为我们的参数，因为当析构函数参数时，没有[的](https://stackoverflow.com/questions/55638953/how-to-handle-null-and-undefined-values-when-destructuring-function-argument)方法来防止`null`和`undefined`值。

让我们在第二次迭代中仔细看看这个检查:

```
if (arguments[1] !== undefined && Object.prototype.toString.call(opts) !== '[object Object]') { 
  throw Error('The options argument must be an object'); 
} 
```

要问的第一个问题是:当 [rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)是一个东西时，我们为什么要使用 arguments 对象？Rest 参数是在 ES6 中引入的，它是一种更简洁的方式，允许开发人员将不定数量的参数表示为一个数组。如果我们使用了它们，我们可能会写出这样的代码:

```
createElement (type, ...args) {
  if (type == null || typeof type !== 'string') {
    throw Error('The element type must be a string');
  }

  if (args[0] !== undefined && Object.prototype.toString.call(args[0]) !== '[object Object]') { 
    throw Error('The options argument must be an object'); 
  }
} 
```

如果我们的函数有很多参数，这段代码会很有用，但是因为我们只需要两个参数，所以前一种方法会更好。关于我们的第二个函数，更令人兴奋的是表达式`Object.prototype.toString.call(opts) !== '[object Object]'`。那个表达式是问题的答案之一:*在 JavaScript 中，如何检查某物是否是对象？*首先尝试的显而易见的解决方案是`typeof opts === "object"`,但是正如我们之前讨论的，它不是一个可靠的检查，因为 JavaScript 错误使用带有`null`值的`typeof`返回`true`。

我们选择的解决方案通过利用内置对象上存在的内部`[[Class]]`属性在 ES5 世界中工作。根据 ES5 [规范](http://www.ecma-international.org/ecma-262/5.1/#sec-8.6.2)，这是一个*字符串值，表示规范定义的对象分类*。使用`toString`方法可以访问它。[规范](http://www.ecma-international.org/ecma-262/5.1/#sec-15.2.4.2)深入解释了`toString`的行为，但本质上，它返回了一个格式为`[object [[Class]]]`的字符串，其中`[[Class]]`是内置对象的名称。

大多数内置函数会覆盖`toString`，所以我们也必须使用`call`方法。这个方法调用一个具有特定`this`绑定的函数。这很重要，因为无论何时调用一个函数，它都是在特定的上下文中调用的。JavaScript 大师 Kyle Simpson 概述了决定 T3 优先顺序的四条规则。第二个规则是，当用`call`、`apply`或`bind`调用函数时，`this`绑定指向`call`、`apply`或`bind`第一个参数中指定的对象。因此`Object.prototype.toString.call(opts)`执行`toString`，而`this`绑定指向`opts`中的任何值。

在 ES6 中，`[[Class]]`属性被移除了，所以虽然解决方案仍然有效，但是它的行为稍微有点[不同](http://www.ecma-international.org/ecma-262/9.0/index.html#sec-object.prototype.tostring)。规范建议不要采用这种解决方案，因此我们可以从[洛达什](https://github.com/lodash/lodash/blob/4.17.11/lodash.js#L3069)处理这种情况的方式中寻找灵感。但是，我们将保留它，因为它产生错误结果的风险非常低。

我们创造了一个表面上看起来很小很基本的功能，但是正如我们所经历的，这个过程一点也不小也不基本。我们可以进入下一个阶段，但这导致了一个问题，这个阶段应该是什么？我们的功能可以做一些测试，但这需要创建一个开发工作流。这会不会太早了？如果我们添加测试，我们将使用哪个测试库？在做任何其他事情之前，创建一个工作解决方案不是更好吗？这些是开发人员每天都要面对的压力，我们将在下一篇教程中探讨这些压力(和答案)。