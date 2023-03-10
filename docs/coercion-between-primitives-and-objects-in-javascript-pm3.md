# JavaScript 中原语和对象之间的强制

> 原文：<https://dev.to/lisaveras/coercion-between-primitives-and-objects-in-javascript-pm3>

每个 JavaScript 开发人员都至少读过一篇文章，描述了使用不匹配的类型执行操作时得到的意外值。这不是其中的一个帖子。所以放轻松，我不会用类型强制的例子来绊倒你。

这篇文章的想法是在我使用 JavaScript 的类型检查器 [Flow](https://flow.org/) 时产生的。在声明类型时，我不小心给了一个类型为 boolean 而不是 Boolean 的参数。我意识到我的错误是偶然的，所以我想谈谈为什么这两件事不一样。

## 弦与弦的区别

JavaScript 中有六种基本类型:布尔型、字符串型、数字型、空型、未定义型和符号型。现在我们将关注前三个。这三个有称为布尔、字符串和数字的包装对象。在进行显式类型转换时，您可能使用过这些特殊的包装对象，例如:

```
 let example = “6”;
    7 === 1 + Number(example)  // true 
```

因为 JavaScript 是松散类型的，所以你总是在这些类型之间使用隐式类型强制，例如:

```
 let example = “This is a string”;
    example.length  // 16 
```

在这里，JavaScript 解释器看到您正试图通过访问一个属性将一个原语用作一个对象。由于原语没有属性，解释器通过在后台调用`String(example)`将字符串原语包装在对象中。这允许您像使用对象一样使用原语。

## 布尔与布尔及其重要性

在很大程度上，我们可以以同样的方式使用原语和包装类型，没有任何问题。但这里有一个特例，它不是真的。

```
 let example = new Boolean(false);
    if (example) {
        console.log(‘Cats are the best’);
    } else {
        console.log(‘Dogs are the best’);
    } 
```

现在就在你的控制台上试试这个例子吧。

你可能会惊讶地看到，它打印出'猫是最好的'！因为在 JavaScript 中所有对象都是真的，所以 example 计算为`true`，这可能是不直观的。

现在您知道了为什么 Flow 不允许您互换使用原语和包装类型的原因之一。我建议在 [Flow 的游乐场](https://flow.org/try/)玩这个。