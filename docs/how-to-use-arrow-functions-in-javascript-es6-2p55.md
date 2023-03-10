# 如何在 JavaScript ES6 中使用箭头函数

> 原文：<https://dev.to/kendalmintcode/how-to-use-arrow-functions-in-javascript-es6-2p55>

[![JavaScript on a computer screen](img/8d16cbb2004dfa6835a74625ed367796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SDqgPWvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/9ea4992a47b7ac036839c70677e2e6d7/4aca8/dlanor-s-703975-unsplash.jpg)

最近，我从精彩的 [Fullstack React 书](https://www.fullstackreact.com/)中学到了很多关于 [React](https://reactjs.org/) 的知识。(这是我极力推荐的，我还在他们的推特账户 [@fullstackreact](https://twitter.com/fullstackreact) 上关注了这个团队)。

这个旅程中令人兴奋的部分之一是 ES6 在整本书中的自由使用。除了标准的 JavaScript 语言风格之外，ES6 语言特性是一种乐趣，它为语言添加了一些新的工具和技术。然而，我发现一个特别有趣的特性是*箭头功能。*

我意识到在这一点上我有点晚了，因为它们几乎不是新的和未知的特性，但我确实发现它们很有趣，并且对使用它们所需的不同语法有点困惑。希望这个指南能帮助我巩固自己的理解，也能帮助那些想掌握箭头函数及其用法的人。

### 什么是箭头功能？

箭头函数(根据 [MDN 关于箭头函数的参考指南](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions))是具有较短语法的简单函数语句；它们为标准的函数构造提供了一个替代方案，它们的主要优势是创建清晰的代码。

它们确实对`this`提供了更多的控制，但是现在，让我们看看它们更短的语法，以及它们如何在生成更简洁、更易读的代码中发挥作用。

### 我们如何使用它们？

很高兴你问了！有三种主要的方法可以构造和使用箭头函数，所以让我们来看一些例子。

**NB -** 在这些例子中，我们将想象我们正在使用一系列冰淇淋口味，如下所示:

`const flavours = [chocolate, strawberry, vanilla, cherry, toffee];`

**多行体带*显式* `return`**

如果你的函数跨越多行，有几个语句，你可以写一个箭头函数如下:

```
const flavourLengths = flavors.map((flavour) => {
    const flavourLength = flavour.length;
    const flavourCapitalised = flavour.charAt(0).toUpperCase() + flavour.slice(1);
    return `${flavourCapitalised} is ${flavourLength} letters`; 
});
console.log(flavourLengths);
// prints -> ["Chocolate is 9 letters", "Strawberry is 10 letters", ...etc] 
```

Enter fullscreen mode Exit fullscreen mode

注意，这里我们必须包含一个显式的`return`语句。更重要的是，如果你只有一个参数，你也可以像这样省略最初的括号:

```
const flavourLengths = flavours.map(flavour => {\
  // ...
); 
```

Enter fullscreen mode Exit fullscreen mode

**单行/——表情体同*`return`***

 *如果你的函数体只包含一行，你可以用括号把它括起来，忽略掉隐含的`return`语句。

```
const flavourLove = flavours.map(flavour => (
    'I love ' + flavour.toUpperCase() + ' ice cream!'
));
console.log(flavourLengths);
// prints -> ["I love CHOCOLATE ice cream", "I love STRAWBERRY ice cream", ...etc] 
```

Enter fullscreen mode Exit fullscreen mode

**单个语句，包括`return`(如超简洁方式)**

更好的是，如果你的语句非常短或者唯一的语句是一个 return，那么你可以把你的 arrow 函数简化成如下:

```
console.log(flavours.map(flavour => flavour.length));
// prints -> [9, 10, 7, 6, 6] 
```

Enter fullscreen mode Exit fullscreen mode

### 那*这个*呢？

在 JavaScript 领域,`this`是一个复杂且经常令人困惑的话题。来自 [MDN 关于箭头功能的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions):

> 在 arrow 函数之前，每个新函数都定义了自己的`[this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)`值(基于函数是如何被调用的，在构造函数的情况下是一个新对象，在[严格模式下](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)函数调用是未定义的，如果函数是作为一个\object 方法被调用，则是基对象，等等。).

传统上声明的匿名函数喜欢将`this`绑定到全局对象，这在执行和开发中都可能导致问题。

箭头函数通过提供一个取自本地上下文的`this`来提供帮助。例如，如果在当前作用域中找不到`this`，它将从封闭作用域中取出。

为了说明这是如何工作的，让我们看两个例子:

**具有全局`this`作用域**的传统函数

```
function printLemurName() {
    console.log('nothing here but us globals');
}

const lemurLand = {
    lemurs: ['Dave', 'John', 'Alice', 'Tanya', 'Lemur No. 5'],
    printLemurName: function(lemur) {
        console.log('This lemur is called ' + lemur);
    },
    printAllLemurNames: function() {
        // Right now, `this` is bound to the lemurLand object, great! 
        this.lemurs.forEach(function(lemur) {
            // but here, `this` is bound to the global object...oh dear
            this.printLemurName(lemur);
        });
    },
};

lemurLand.printAllLemurNames();
// 'nothing here but us globals'
// 'nothing here but us globals'
// 'nothing here but us globals'
// 'nothing here but us globals'
// 'nothing here but us globals' 
```

Enter fullscreen mode Exit fullscreen mode

**同样的例子，用箭头函数**代替了

```
//...same preceding code

    printAllLemurNames: function() { 
        this.lemurs.forEach(lemur => {
            // ahh that's better, `this` is correctly set to the lemurLand object
            this.printLemurName(lemur);
        });
    },
};

lemurLand.printAllLemurNames();
// 'This lemur is called Dave'
// ...etc.
// 'This lemur is called Lemur No. 5' 
```

Enter fullscreen mode Exit fullscreen mode

### 箭头还有更多功能

这是一个快速介绍指南，介绍什么是 arrow 函数，它们在干净、可读的代码编写中扮演什么角色，以及它们可以通过有益的应用`this`解决什么问题。

然而，它们确实有一些其他的高级用途，我强烈推荐阅读 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions),以获得更多的例子、用例以及对 arrow 函数更深入的技术概述。*