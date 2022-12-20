# JavaScript:这是什么？！

> 原文：<https://dev.to/emiko/javascript-what-is-this-27kl>

# JavaScript:`this`是什么？！

*当你阅读这篇博文时，打开你的开发者控制台(Windows: Ctrl + Shift + J 或 Mac: Cmd + Option + J)和我一起编码可能会有帮助。*

# 概述:`this`

在 Javascript 中，`this`是关于上下文的，而*这个*可能非常令人困惑。我所说的☝是指`this`的值依赖于它的执行上下文(即:在哪里调用`this`)，这意味着依赖于`this`在哪里被执行/调用，什么`this`引用将会改变。这就是`this`如此有用的原因。为了理解`this`的值，我们需要查看调用点(执行上下文)。
试着将这段代码粘贴到您的控制台:

```
console.log(this); 
```

在您按下回车键之后，您应该会看到类似这样的内容:

```
Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …} 
```

这意味着`this`指的是全局窗口对象。这是有意义的，因为我们从控制台(它的执行上下文)调用了`this`，默认情况下它指的是全局窗口对象。
在一个简单的函数调用中，`this`的执行上下文默认引用全局(窗口)对象。
这里有一个例子:

```
function hey () {
 console.log("Hey, I'm a simple function call");
 console.log(this === window); 
}
hey() 
```

你应该在你的控制台上看到这个:

```
Hey, I'm a simple function call
true 
```

这里，在一个简单的函数调用中，`this === window`，这就是为什么 console.log 返回为`true`。
现在把这个放进你的控制台:

```
var animal = { 
 cat : function() {
 console.log(this)
 console.log(this.sound)
 },
 sound: "hrrrrmph"
}
animal.cat() 
```

您应该会在您的控制台中看到类似这样的内容:

```
cat: ƒ, sound: "hrrrrmph" 
```

其次是:

```
hrrrrmph 
```

在上面的上下文中，`this`是指动物对象，`this.sound`成为动物对象的声音值(“hrrrrmph”)。
好吧，让我们更上一层楼！

```
var animal = {
bear: function () {
 var sound = "ROARRRR"

 this.cat()
 },
cat : function() {
 console.log(this.sound)
 },
 sound: "hrrrrmph"
}
animal.bear() 
```

你认为当我们运行上面的代码时会发生什么？
在这里，通过调用`animal.bear()`，我们调用登录到我们控制台的`this.cat()`“hrrrrmph”。这是因为`this.cat()`中的的执行上下文是 animal。所以叫`animal.bear()`就叫`animal.cat()`哪个`console.logs(this.sound)`。
如果我们把 var sound = "ROARRRR "从 bear 移到 cat 会怎么样？

```
var animal = {
bear: function () {
 this.cat()
 },
cat : function() {
 var sound = "ROARRRR"
 console.log(this.sound)
 },
 sound: "hrrrrmph"
}
animal.bear() 
```

你猜我们还是会得到“hrrrrmph”吗？！
我没有！但是就像我之前说的，*这个*很复杂！让我们记住我之前说过的另一件事。`this`都是关于上下文(确切地说是执行上下文)。让我们一起走过它。
当调用 animal.bear()时，`this.cat()`中的`this`就是动物对象。所以`console.log(this.sound)`里的`this`就是动物对象！这是调用被执行的地方，这意味着我们正在控制台记录`animal.sound`。
下面这段代码呢？试着弄清楚`this`的执行上下文。

```
var animal = {
bear: function () {
 this.cat()
 },
cat : function() {
 var sound = "ROARRRR"
 console.log(this.sound)
 },
 sound: "hrrrrmph"
}
animal.cat() 
```

`this`仍指动物物体！所以，我们还是得到`hrrrrmph`
好了，让我们试着深入到`this` :

```
var animal = 'Cat'
var forest = {
 animal: 'Bear',
 ocean: {
 animal: 'Pelican',
 animalName: function() {
 return this.animal
 }
 }
}
forest.ocean.animalName() 
```

好了，在我们继续看上面的代码会返回什么之前，先说一下每个动物的作用域。

*   **猫**🐱属于全球🌐窗户。
*   **熊**🐻属于森林🌲范围。
*   **鹈鹕**🐦属于海洋🌊现在回到代码上。你认为`this`在上下文`forest.ocean.animalName()`中是指什么？这是`forest.ocean`的对象！所以，this.animal(与 forest.ocean.animal 相同)返回“Pelican”，因为鹈鹕属于海洋。很好。如果我们把这个添加到控制台的代码中会怎么样:

```
var animalName = forest.ocean.animalName 
```

让`animalName`成为一个返回`this.animal`的函数，现在，在你的控制台上运行这段代码:

```
animalName() 
```

你认为我们会看到什么回报？`this`的执行上下文是什么？如果你说的是全球，那你就对了！运行代码`animalName()`相当于运行 this.animal，而由于我们是从全局窗口执行这段代码，所以就像调用 window.animal 一样，也就是“猫”！
我怎样才能使用 animalName()(它返回`this.animal`)来返回“熊”？

# 📞电话

那就是。call()函数进来了。这里是 W3Shool 关于。call():
** *通过 call()，一个对象可以使用属于另一个对象的方法。** *
听起来很简单。让我们把它编码进去！既然我们从☝那里知道熊属于森林，那我们就把森林交给老师吧。调用函数。

```
animalName.call(forest) 
```

还有 TA-DAAA，我们得到“熊”
如果我们执行这段代码会得到什么:

```
animalName.call(window) 
```

没错，我们会得到“猫”。
现在我们已经掌握了`this`，让我们来谈谈什么时候使用`this`对我们不起作用。

# ➡️箭头功能和`this`

箭头函数是一种语法上更简洁的编写函数表达式的方式，有些人认为它更容易阅读。你可以在这里和这里阅读更多关于箭头功能[的内容。但是这里有一个最简短的概述:
一个正则函数是这样写的:](https://medium.freecodecamp.org/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26) 

```
function animalName () {
 return this.animal
} 
```

下面是同一个函数与箭头函数的关系:

```
arrowAnimalName => () => this.animal 
```

他们两个我都喜欢，但我是双子座的，所以这是意料之中的。
但是你会问，为什么我要提出与`this`相关的箭头函数。
这是因为在箭头函数中`this`是词汇绑定的——这意味着`this`永远不会改变😱。
[FreeCodeCamp 上的 Cynthia Lee](https://medium.freecodecamp.org/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26) 是这样分解的:
** *在经典的函数表达式中，this 关键字会根据调用它的上下文绑定到不同的值。然而，对于 arrow 函数，这是有词汇限制的。这意味着它从包含 arrow 函数的代码中使用它。*
**
意思是我们不能用。调用()或。应用()或。用箭头函数绑定()，因为`this`在词汇上被绑定到执行箭头函数的地方。
所以如果我们运行

```
arrowAnimalName.call(forest) 
```

我们将得到“Cat ”,因为`this`在词汇上被绑定到我们定义这个箭头函数的全局窗口。
所有的`this`都会变得很混乱，也许会感到不知所措。但是你知道吗？我们总是有`console.log(this)`和`debugger`来帮助我们发现`this`到底是什么，这让我感觉很好🌈。
感谢阅读！