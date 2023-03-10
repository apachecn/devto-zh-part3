# Javascript 中的析构

> 原文：<https://dev.to/mindsers/destructuring-in-javascript-1i8k>

析构适用于数组和对象。它在于分解复杂的结构(数组、对象)来提取我们感兴趣的值。这些操作使得代码更简单、更易读。

```
let [valeur1, valeur2] = tableau
let { valeur1, valeur2 } = objet 
```

Enter fullscreen mode Exit fullscreen mode

无论是数组还是对象，语法都是不同的。

## 打破数组向下

分解数组的一个具体例子是当你想从一个数组中获取值的时候。

```
let args = ['fruits', 'pomme', 'poire', 'abricot']
let type = args[0]
let firstEl = args[1]
let secondEl = args[2]
let thirdEl = args[3] 
```

Enter fullscreen mode Exit fullscreen mode

在没有析构的情况下，下面是我们如何从数组中获取值来使用它们。

```
let args = ['fruits', 'pomme', 'poire', 'abricot']
let [type, firstEl, secondEl, thirdEl] = args 
```

Enter fullscreen mode Exit fullscreen mode

解构也是一样。

这两位代码是相似的，结果是一样的。在每种情况下，都会创建变量`type`、`firstEl`、`secondEl`、`thirdEl`。

如果我们就此打住，除了节省我们三行代码之外，这种语法没有太大的意义。但是析构有更多的东西可以让你更有效率。

我们不会每次都与没有析构的例子进行比较，但是试着想一想，如果没有析构，你将如何编写相同的特性。

**例子:**如果我只想把类型和第二个元素拿回来。

```
let args = ['fruits', 'pomme', 'poire', 'abricot']
let [type,, secondEl] = args 
```

Enter fullscreen mode Exit fullscreen mode

你只需要在`args[0]`和`args[2]`之间添加一个彗差，而不需要指定`args[1]`的变量名。

**示例:**如果`args`为空，我的变量`type`和`secondEl`将被创建，但值为`undefined`。为了能够继续使用它，我必须测试我的变量。但是我也可以给它们赋予默认值。

```
let args = []
let [type = 'legume', firstEl = 'salade'] = args 
```

Enter fullscreen mode Exit fullscreen mode

由于`args`为空，`type`将使用“légume”作为值，`firstEl`将使用“salade”作为值。

**例如:**如果我想返回到变量中元素的类型，但对我来说更容易的是在一个数组中循环元素的列表。

```
let args = ['fruits', 'pomme', 'poire', 'abricot']
let [type, ...elements] = args 
```

Enter fullscreen mode Exit fullscreen mode

这里,`type`有“果实”作为值,`elements`有`['pomme', 'poire', 'abricot']`。

这里的语法很特别，因为它使用了我们所说的 rest 参数。如果你想了解更多关于 rest paramater 的信息，你可以阅读这篇文章(法语)。

如果您通常不会想到析构，也可以使用析构:

**举例:**如果我要交换两个变量的值。

```
let valA = 'coucou'
let valB = 'byebye'
[valA, valB] = [valB, valA] 
```

Enter fullscreen mode Exit fullscreen mode

从逻辑上讲，`valA`的值为“拜拜”,`valB`的值为“coucou”。

**示例:**析构可以用于处理返回数组的函数。

```
let [max, min] = getMaxAndMin([3, 34, 2, 5, 6, 22, 33, 1]) 
```

Enter fullscreen mode Exit fullscreen mode

这里感兴趣的是代码的可读性。当然，您需要确定所用函数返回值的顺序。

**举例:**可以使用析构作为函数参数。

```
function firstIsTrue([first = false]) {
  return first === true
} 
```

Enter fullscreen mode Exit fullscreen mode

`firstIsTrue()`接受参数中的数组。只有第一个值将被传递给函数，如果数组为空:`first`将把`false`作为值。

对于函数用户来说，一切都是透明的。

## 破坏物体

析构对象与数组非常相似，只是有一些特殊之处。我们将很快谈到这一点，以停止更有趣的概念。

**示例:**检索我们的一个密钥

```
let objet = {
  slug: 'test'
  title: 'Ceci est un test'
  content: '...'
  visible: true
}

let { title } = objet 
```

Enter fullscreen mode Exit fullscreen mode

我们只想要标题的标准例子。你会注意到，当它是一个对象时，我们用括号代替方括号。

**举例:**默认值

```
let objet = {
  slug: 'test'
  title: 'Ceci est un test'
  content: '...'
  visible: true
}

let { visible = false } = objet 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，您只需要可见性和一个`false`。

**例子:**使用一个键的另一个名字

```
let objet = {
  slug: 'test'
  title: 'Ceci est un test'
  content: '...'
  visible: true
}

let { content: article } = objet 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们只检索存放在变量`article`中的键`content`。

<mark>析构的真正语法就是这个例子中使用的语法。在其他例子中，我们使用了 ES2015 中的一个技巧，该技巧可以防止我们尽可能重复编写相同的内容。只是提醒一下`{ visible: visible }`等于`{ visible }`。</mark>

**例子:**默认值累计和名称变更

```
let objet = {
  slug: 'test'
  title: 'Ceci est un test'
  content: '...'
  visible: true
}

let { visible: isVisible = false } = objet 
```

Enter fullscreen mode Exit fullscreen mode

我们检索存放在变量`isVisible`中的键`visible`。这个变量将默认为`false`。

**示例:**使用析构作为函数参数

这是我使用析构最多的情况。它节省了很多时间。

您可能已经编写了一些带有`options`参数的函数，将您所有的选项都集中在一个对象中。想想在编写业务逻辑之前，为了测试这个参数，您浪费了多少时间和多少行代码。

```
function test(id, { maxLength = 10, current = 0 } = {}) 
```

Enter fullscreen mode Exit fullscreen mode

至于析构一个数组，在 paramaters 中使用它对用户来说是绝对透明的，但是它改变了你的整个生活。

`maxLength`和`current`永远不会是`undefined`，将总是有一个值，要么是用户值，要么是默认值。这将减轻你的代码，因为你将不再做`options.maxLength`，而只是做`maxLength`，就好像它是一个简单的参数`id`。

只有在析构中声明的属性才会被传递到函数中。用户可以给`options`添加 15 000 个其他属性，它们不会被传输。它也为您的功能增加了一点安全性。

## 结论

我真的希望我说服你尝试它。对我来说，析构是 ES2015 最大的新特性之一。

这是一个强大的功能，可以让你的代码更简单，更清晰，更漂亮，更干净...你知道我有多喜欢干净的代码。

一些可以深入了解的资源:

*   [理解`fetch`，`jQuery.ajax`替代](https://blog.nathanaelcherrier.com/2016/10/26/fetch-fin-jquery-ajax/)(法语)
*   [理解 Javascript](https://blog.nathanaelcherrier.com/2016/11/09/rest-parameter-et-spread-operator-en-javascript/) 中的析构运算符和 rest 参数(法语)
*   [了解脱钩](https://blog.nathanaelcherrier.com/2016/08/30/decouplez-votre-code/)(法语)