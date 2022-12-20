# 通过首字母缩略词解释浅/深复制

> 原文：<https://dev.to/michi/explaining-shallow-deep-copying-through-acronyms-2ia0>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/shallow-copying-explained-in-simple-terms)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

为了理解浅/深复制，让我们暂时离开键盘，看看一般的尺寸。

其实我们来看看首字母缩略词`ajax`。它实际上代表什么？

> `Asynchronous JSON and XML`。

等待...所以首字母缩略词`ajax`由另外两个首字母缩略词`JSON`和`XML`组成。
换句话说，缩略词`ajax`有第二维度的缩略词，这使它成为一个多维度的缩略词！😱

因此，当我们不省略`ajax`到`Asynchronous JSON and XML`时，我们只不省略第一维，换句话说:浅层不省略。这个词现在可能还不存在，但很快就会被收入词典。注意第二维度`JSON`和`XML`是如何保持不变的。我们只是引用这些其他的缩写。

如果我们对`ajax`进行深度删节，我们会得到这样的结果:

> 异步 JavaScript 对象符号和可扩展标记语言

想象一下，在过去，我们不得不写
`$.asynchronousJavaScriptObjectNotationAndExtensibleMarkupLanguage`

多维首字母缩略词的另一个例子是 JAM stack。

浅薄的-不简略的:

> JavaScript API 标记

深度非缩写:

> JavaScript 应用程序编程接口标记

* * *

所以，让我们离开这些名字很不幸的缩写词，进入代码。

```
const ajax = {
  a: 'Asynchronous',
  j: {
    j: 'Java',
    s: 'Script',
    o: 'Object',
    n: 'Notation'
  },
  a2: 'and',
  x: {
    x: 'Extensible',
    m: 'Markup',
    l: 'Language'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们已经把`ajax`布置在一个二维物体中。

如果我们将这个对象复制到另一个对象中会发生什么

```
const obj = ajax
obj.x = null

obj.x //? null
ajax.x //? null 
```

Enter fullscreen mode Exit fullscreen mode

这样不行。`obj`将仅仅是对`ajax`的引用。改变一个就会分别改变另一个。这就是 JavaScript 中对象的工作方式。

这个怎么样？

```
const obj = Object.assign({}, ajax) 
// or: const obj = {...ajax}

obj.x = null

obj.x //? null
ajax.x //? { x: 'Extensible', m: 'Markup', l: 'Language' } 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们创建了一个真实的副本！还是我们...？

```
const obj = Object.assign({}, ajax)

obj.x.l = 'lang'

obj.x.l //? lang
ajax.x.l //? lang 
```

Enter fullscreen mode Exit fullscreen mode

原来`Object.assign`和 ES6 spread 语法仅仅是做了一个肤浅的复制！

那么我们怎么可能复制整个对象，即深度复制呢？

您经常看到的一个相当粗糙的解决方案是下面的

```
const obj = JSON.parse(JSON.stringify(ajax)) 
```

Enter fullscreen mode Exit fullscreen mode

虽然这在我们的例子中是可行的，但它只是删除了对象上的任何方法。它也不能在地图和布景上工作。

可悲的事实是，JavaScript 并没有提供现成的这种功能。您可以创建自己的深度复制方法，也可以利用现有的解决方案。

PS。在 JavaScript 中，数组是对象，所以我们讨论的一切也适用于数组。