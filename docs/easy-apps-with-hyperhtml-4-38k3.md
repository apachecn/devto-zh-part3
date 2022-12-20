# 具有 hyperHTML-4、连线类型和意图的简单应用程序

> 原文：<https://dev.to/pinguxx/easy-apps-with-hyperhtml-4-38k3>

西班牙文版

第 4 部分作者

[![pinguxx image](img/b3fd6923e5679952858c6b2c43518de0.png)](/pinguxx)

## [伊凡](/pinguxx)

[standard geek](/pinguxx)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)pinguxx](https://twitter.com/pinguxx)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)pinguxx](https://github.com/pinguxx)

[![paritho image](img/31ca3c53383636df8ffd826c26771850.png)](/paritho)

## [保罗·汤普森](/paritho)

[lover of dogs and javascript. and coffee. and writing.](/paritho)

[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)帕里托](https://github.com/paritho)

1.  [介绍，焊线/绑定](https://dev.to/pinguxx/easy-apps-with-hyperhtml-1-31cc)
2.  [事件和组件](https://dev.to/pinguxx/easy-apps-with-hyperhtml-2-hbi)
3.  [关于组件和简单状态管理的 Moar】](https://dev.to/pinguxx/easy-apps-with-hyperhtml-3-1m3l)
4.  **电线类型和自定义定义(意图)**
5.  [用 hyper 定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-5-13hc)
6.  [定制我的定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-6-1e73)
7.  [测试！](https://dev.to/pinguxx/easy-apps-with-hyperhtml-7-5b9g)
8.  [异步加载、占位符和带超链接的提前输入](https://dev.to/pinguxx/easy-apps-with-hyperhtml-8-async-loading-5d96)
9.  [搬运路线](https://dev.to/pinguxx/easy-apps-with-hyperhtml-9-routing-59k0)
10.  [第三方库](https://dev.to/pinguxx/easy-apps-with-hyperhtml-10-3rd-party-libraries-2n7b)

在本教程的前几部分，我们已经看到了如何在 hyperHTML 中使用组件。我们构建了一个可以排序的简单表格，但是现在是时候看看 hyperHTML 带给我们的一些附加特性了。我们接下来要看的特性是定制 wire()的能力。使用定制的 wire()函数，我们将能够转换我们的模板，或者异步加载它们的一部分。在我们深入研究这个之前，让我们更深入地研究一下标准的 wire()函数。

* * *

### **内置类型**

HyperHTML wire()有两种内置类型。它的默认是 html，但是 hyperHTML 也支持 svg。正如我们已经看到的，使用 html 类型不需要我们做额外的工作。然而，如果我们想要连接一个 svg 节点，我们需要明确地这样说:

```
hyperHTML.wire(data, 'svg')`<rect width=${data.width} height=${data.height} />` 
```

传递给 wire 的第二个参数是类型。让我们看看运行的示例:

[https://stackblitz.com/edit/wire-types-svg?embed=1&&](https://stackblitz.com/edit/wire-types-svg?embed=1&&)

一直回忆到[第一部](https://dev.to/pinguxx/easy-apps-with-hyperhtml-1-31cc)。在我们的时钟示例中，我们以两种方式使用 wire()。首先，我们在没有参考的情况下使用了它:

```
hyperHTML.wire()`…` 
```

但是我们也通过向函数传递一个对象来使用它:

```
hyperHTML.wire(user)`…` 
```

在第一个例子中，我们提到传递给 wire()的对象不会在每次渲染时更新。这是因为通过将它传递给 wire()，我们在 wire 和对象的内容之间创建了一种关系。这种关系不仅仅是加速渲染，我们还可以在不同的地方使用不同的模板重用这个对象。然后，我们可以给这个对象一个 id，用于我们想要使用它的每个地方:

```
hyperHTML.wire(user, ':address')`…`
hyperHTML.wire(user, ':profile')`…`
hyperHTML.wire(user, ':login')`…` 
```

[https://stackblitz.com/edit/wire-types-user?embed=1&&](https://stackblitz.com/edit/wire-types-user?embed=1&&)

我们还可以在 id 旁边指定一个类型:

```
hyperHTML.wire(user, 'svg:avatar') 
```

#### 内容值

在第一部分中，我们也谈到了不同的内容值。现在让我们更深入地研究一下。

想想模板文字。在模板文本中，我们使用${…}来计算表达式。您传递到${…}中的任何表达式都将被添加到您的模板中，这取决于它的计算类型。例如，hyperHTML 在默认情况下是注入安全的，因为传递的字符串将作为`textContent.`
注入模板

```
<p>Hola ${'<script src="http://badguy.com"></script>'}</p> // whew, safe! 
```

但也可以强制为文本。为此，您必须将一个对象传递给 hyperHTML:

```
<p>Hola ${{text: 'Mundo'}}</p> 
```

是的，如果你传递一个节点，它将被追加:

```
<p>Hola ${node}</p> 
```

或者你可以通过传递一个对象来强制它，就像上面:

```
<p>Hola ${{html: '<strong>Mundo</strong>'}}</p> 
```

你甚至可以通过一个承诺！如果你传递了一个承诺，那么每当这个承诺被解决时，它将被解决成任何一种被理解的类型。还有一种类型，any。Any 通常接受承诺，但也可以接受任何其他类型，hyperHTML 将尝试匹配它。

观看他们的行动:

[https://stackblitz.com/edit/wire-values?embed=1&&](https://stackblitz.com/edit/wire-values?embed=1&&)

但是等等，还没完呢！也可以传数组！唯一需要记住的是，数组*中的项目必须是同一类型:字符串、数字或节点，甚至是承诺列表。如果您的数组填充了不同的类型，您会得到意想不到的结果。*

 *[https://stackblitz.com/edit/wire-array?embed=1&&](https://stackblitz.com/edit/wire-array?embed=1&&)

当我们现在没有数据，但被承诺将来会有数据时，我们通常会使用承诺。HyperHTML 提供了在加载数据时显示的占位符。让我们看一个例子。

[https://stackblitz.com/edit/wire-placeholder?embed=1&&](https://stackblitz.com/edit/wire-placeholder?embed=1&&)

#### 属性

元素属性可以是常规属性、布尔值或事件。如果你从另一个框架进入 hyperHTML，你可能期望使用部分属性，但是[你实际上并不需要它们](https://viperhtml.js.org/hyperhtml/documentation/#essentials-7)。我们还需要讨论两种属性类型——样式和数据。这些属性将帮助我们轻松地构建接口。

style 属性可以像其他属性一样接受一个字符串，也可以传递一个对象:

```
wire(ref)`<p style=${{fontSize: 32}}>${'BIG CONTENT'}</p>`; 
```

data 属性允许您将原始 JavaScript 数据传递给一个元素:

```
wire(ref)`<p data=${user} />`; 
```

让我们看看他们的行动

[https://stackblitz.com/edit/wire-attributes?embed=1&&](https://stackblitz.com/edit/wire-attributes?embed=1&&)

* * *

### 自定义类型

我们可以利用对象功能并创建自定义呈现部分。根据名称的不同，它将被解释为一个属性(如果它有'-')，或者一个新的意图。

#### 自定义意图

为了定义一个新的类型，我们将使用`hyperHTML.define()`。方便吧？Define()接受两个参数，一个字符串名称和一个回调。

```
define(intentName, callback) 
```

回调函数接收任何插值数据作为参数，并返回我们将在显示中使用的数据。回调可以返回任何已知的数据类型。

例如，让我们定义日期的意图。每当我们收到一个日期，我们将返回一个格式良好的日期。让我们也使用异步意图:

[https://stackblitz.com/edit/wire-custom?embed=1&&](https://stackblitz.com/edit/wire-custom?embed=1&&)

HyperHTML 将首先尝试匹配任何已知的意图，如 HTML、文本或任何。如果它找不到匹配，它将尝试使用它的注册表中的那些，也就是用`hyperHTML.define()`定义的那些。如果 hyper 在那里找到它，它将使用您的自定义意图。

#### 自定义属性

如前所述，如果在名称中用'-'定义意图，它将被视为一个属性。这意味着我们可以向任何元素添加任何自定义属性。

哇哦。真的吗？

是的，你打赌。

在我们的下一个例子中，回调将接收节点元素和传递给属性的任何值。如果你返回一些东西，它将被用作属性的值。请务必注意，要使定制属性工作，您必须使用`${…}`作为属性值。

[https://stackblitz.com/edit/wire-custom-attribute?embed=1&&](https://stackblitz.com/edit/wire-custom-attribute?embed=1&&)

* * *

根据我们现在所知道的，让我们从第三部分的[开始更新我们的表格。](https://dev.to/pinguxx/easy-apps-with-hyperhtml-3-1m3l)

[https://stackblitz.com/edit/basics-table-6?embed=1&&](https://stackblitz.com/edit/basics-table-6?embed=1&&)

我们将添加一个 svg 图标，这样我们就知道当前对哪一列进行了排序，以及它是升序还是降序。让我们用更准确的用户信息和日期更新我们的数据数组，这样我们就可以很好地利用我们的日期意图。

[https://stackblitz.com/edit/basics-table-7?embed=1&&file=intents.js](https://stackblitz.com/edit/basics-table-7?embed=1&&file=intents.js)

我们正在使用 [Octicons](https://octicons.github.com/) 图标包。现在来看看剩下的代码。特别要确保看一下头文件和表文件。

[https://stackblitz.com/edit/basics-table-7?embed=1&&file=Header.js](https://stackblitz.com/edit/basics-table-7?embed=1&&file=Header.js)

* * *

太棒了。现在你知道超 HTML 有多强大了。这几乎是关于库本身的所有内容，但是我们将利用 Andrea Giammarchi 提供的一些其他实用程序来为我们的 belt 添加更多工具。将所有这些工具结合起来，我们将能够创建出色的高性能 web 应用程序。*