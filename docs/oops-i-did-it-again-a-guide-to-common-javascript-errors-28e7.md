# 哎呀，我又做了:调试常见 JavaScript 错误指南

> 原文：<https://dev.to/aspittel/oops-i-did-it-again-a-guide-to-common-javascript-errors-28e7>

编写 JavaScript 代码有时会让我们觉得想要逃跑、躲藏或者只是害怕。但是，通过一些调试技巧，我们可以进入状态，然后一直跳到世界末日！

## 类型错误:无法读取未定义的属性“lucky”

```
let girl = {
    name: "Lucky",
    location: "Hollywood",
    profession: "star",
    thingsMissingInHerLife: true,
    lovely: true,
    cry: function() {
        return "cry, cry, cries in her lonely heart"
    }
}

console.log(girl.named.lucky) 
```

这段代码抛出错误“未捕获的类型错误:无法读取未定义的属性‘lucky’”。在我们的`girl`对象中，我们没有属性`named`，尽管我们有名字。因为`girl.named`是未定义的，我们不能访问不存在的东西的属性。所以，就像那个叫 Lucky 的女孩一样，我们的生活(或物品)缺少了一些东西。我们想把`girl.named.lucky`改成`girl.name`，作为回报，我们会得到“幸运”！

> *属性*是 JavaScript 对象中的一个值。你可以在这里阅读更多关于物品[的信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)。

### 调试类型错误的步骤

TypeErrors 是由于试图对没有与所述操作匹配的数据类型的东西执行操作而导致的。因此，尝试在一个数字上运行`.bold()`，在`undefined`上检索一个属性，或者尝试运行类似函数而不是函数的东西(例如`girl()`会抛出一个错误——女孩是一个对象，而不是函数)。对于最后两个，我们会得到“未捕获的类型错误:yourVariable.bold 不是函数”和“女孩不是函数”。

为了调试这些错误，你需要检查你的变量——它们是什么？什么是`girl`？什么是`girl.named`？这是应该的吗？您可以通过查看您的代码、`console.log` -ing 您的变量、使用`debugger`语句或者只是在控制台中键入变量并查看它是什么来检查这一点！确保您可以对变量的数据类型执行操作。如果没有，转换变量的数据类型，添加一个条件或 try/catch，以便只在某些时候运行该操作，或者在其他地方运行该操作！

## 堆栈溢出

[根据《宝贝再来一次》的词曲作者](https://www.billboard.com/articles/columns/pop-shop/6753845/hit-me-baby-one-more-time-nsync-star-meaning-revealed),《打我宝贝，再来一次》中的“打”指的是打电话，所以布兰妮希望她的前伴侣再给她打一次电话。这可能会导致未来的呼叫越来越多。这反映了递归——如果调用堆栈大小溢出，将会导致错误。

这些因浏览器而异，但可能看起来像:

```
Error: Out of stack space (Edge)
InternalError: too much recursion (Firefox)
RangeError: Maximum call stack size exceeded (Chrome) 
```

这可能是因为没有递归基本情况，或者有一个从不触发的基本情况。

```
function oneMoreTime(stillBelieve=true, loneliness=0) {
    if (!stillBelieve && loneliness < 0) return
    loneliness++
    return oneMoreTime(stillBelieve, loneliness)
} 
```

在上面的函数中，`stillBelieve`永远不会变成`false`,孤独感不断增加，所以我们一直递归调用`oneMoreTime`,从未退出函数。

如果我们让布兰妮依赖她的朋友，而不是减少她的孤独感，并且她不再相信这段关系，她将不再希望她的前伴侣打电话。

```
function oneMoreTime(stillBelieve=true, loneliness=0) {
    if (!stillBelieve && loneliness < 0) return
    loneliness--
    stillBelieve = false
    return oneMoreTime(stillBelieve, loneliness)
} 
```

无限循环也有类似的问题，尽管我们的页面通常不会出现错误消息，而是死机。当我们有一个未终止的`while`循环时，就会发生这种情况。

```
let worldEnded = false

while (worldEnded !== true) {
  console.log("Keep on dancin' till the world ends")
} 
```

我们可以用类似的方法解决这个问题！

```
let worldEnded = false

while (worldEnded !== true) {
  console.log("Keep on dancin' till the world ends")
  worldEnded = true
} 
```

### 调试无限循环和无限递归

首先，如果你在一个无限循环中，如果你在 Chrome 或 Edge 中退出标签页，在 FireFox 中退出浏览器窗口。然后，检查一下你的代码:是不是有什么东西明显地造成了无限循环/递归？如果没有，我会在循环或函数中添加一个`debugger`语句，并确保变量在最初的几次迭代中是它们应该的样子——您可能会注意到某个东西被关闭的模式。在上面的例子中，我会在函数或循环的第一行放一个`debugger`。然后，我会进入 Chrome 的调试选项卡，查看`Scope`中的变量，然后，我会点击“下一步”按钮，看看在一两次迭代后它们是什么。通常这能让我们找到解决方案！

这个是用 Chrome 的 DevTools 调试的很好的指南，[这里有一个用于 FireFox 的](https://developer.mozilla.org/en-US/docs/Tools/Debugger)指南。

## 未捕获的语法错误:意外的标识符

JavaScript 中最常见的错误可能是`SyntaxError`的——这意味着我们没有遵循 JavaScript 的语法规则。按照布兰妮在《每时每刻》中的观点，JavaScript 对我们缺少的圆括号、方括号和引号说“我想我需要你，宝贝”。

我会确保你有一个好的文本编辑器主题或扩展安装，如果你与这些类型的错误斗争——括号对着色帮助彩色代码括号和括号，漂亮或另一个 linter 可以帮助捕捉这些错误快。此外，确保适当缩进代码，并尽可能保持代码块的简短和不嵌套。这将使调试任何问题更容易！

* * *

现在，有了新的调试技能，你会觉得 JavaScript 比昨天强了一点。如果你想给我更多的流行文化代码参考，[下面是](https://dev.to/aspittel/thank-u-next-an-introduction-to-linked-lists-4pph) thank u next:链表介绍。