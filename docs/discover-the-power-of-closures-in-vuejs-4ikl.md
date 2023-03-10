# 在 VueJS 中发现闭包的力量

> 原文：<https://dev.to/hinsencamp/discover-the-power-of-closures-in-vuejs-4ikl>

最初发表于[hinsencamp.com](https://hinsencamp.com/article/closures-vue/)

今天的前端技术前景，要求工程师了解各种各样的技术，包括框架、库和包。

不足为奇，普通 JavaScript 技能和深入的知识可能会开始分散。不管你是刚学 JavaScript，还是在刷新基础知识，还是在准备求职面试→这个教程就是为你准备的！

在这里，您将了解到平面 JavaScript 闭包是多么强大。请注意，本教程带有一个挑战。💪这一切都是为了在 VueJS 中构建一个优雅的表情选择器，并通过使用高阶函数来利用闭包。

让我们开始吧！

## 功能范围

尽管闭包是 JavaScript 中最强大的概念之一，但它很容易被许多人忽略。

然而，了解闭包是最基本的，因为它们定义了函数在执行过程中可以访问哪些变量。更准确地说，闭包定义了一个函数从自己的作用域开始，通过所有父作用域直到全局作用域可以访问的作用域。

要真正掌握闭包，首先必须对作用域有一个坚实的理解。很可能，你自己也已经体验过 scopes 的影响。每次你执行一个函数，一个作用域就会被创建。当你在函数中创建变量时，这些变量只能从函数内部访问。

当一个函数完成时(通过到达`return`语句或`}`，所有这些变量都被销毁。下次执行该函数时，将应用相同的过程。

让我们看下面的例子来说明这个概念。

```
function square(x) {
  const squareX = x * x;
  console.log(squareX); // 25
  return squareX;
}

const squareA = square(5);

console.log(squareA); // 25
console.log(squareX); // ReferenceError: squareX is not defined. 
```

Enter fullscreen mode Exit fullscreen mode

把作用域想象成只有该函数中的代码可以访问的临时上下文。

虽然作用域的生命期非常有限，受到函数执行所需时间的限制，但相比之下，函数的闭包在函数最初定义时就已经创建了。它也将在执行完成后保留。

## 关闭

如前所述，闭包负责定义在函数执行的范围内哪些变量是可访问的。理解这一点很重要，闭包不提供可用变量的副本，而是提供对它们的引用。如果您不熟悉 JavaScript 的参考资料，请查看这篇文章。

```
let globalString = "A";

function hello() {
  const localString = "C";
  console.log(globalString, localString);
}
hello(); // A C

globalString = "B";

hello(); // B C 
```

Enter fullscreen mode Exit fullscreen mode

这个例子看起来很熟悉，没有什么特别的，这解释了为什么我们几乎没有意识到闭包有多强大，因为只在全局范围内定义函数是很常见的。

然而，当函数在另一个函数的作用域内定义时，闭包支持强大的技术和模式。在面向对象的架构中，闭包提供了一种简单而有效的方法来建立数据隐私。在更多的函数式架构中，闭包对于**高阶函数**和**部分应用**和**curry**是必不可少的，这是两种更高级的编程技术。在这里阅读更多关于这些技术的内容。

## 高阶函数

对其他函数进行操作的函数，无论是将它们作为自变量还是通过返回它们，都被称为**高阶函数**。

```
function createMultiplier(multiplier) {
  return function(value) {
    return value * multiplier;
  };
}

const multiplyBy2 = createMultiplier(2);

console.log(multiplyBy2(5)); // 10 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们终于可以体验理解闭包的力量。即使`createMultiplier`已经成功完成。我们仍然可以访问它的初始`multiplier`属性。

这是可能的，因为闭包保存了变量的引用，它们甚至可以跨越多个作用域，并且在上下文终止时不会被破坏。这样，我们仍然可以访问局部变量的特定实例。

## 数据隐私

```
function privateVariables() {
  let privateValue = 100;
  return {
    get: function() {
      return privateValue;
    }
  };
}
console.log(privateVariables.get()); //100 
```

Enter fullscreen mode Exit fullscreen mode

为什么闭包能让我们实现数据隐私？

我们可以简单地封闭变量，只允许包含(外部)函数范围内的函数访问它们。

除非通过对象的特权方法，否则不能从外部作用域获取数据。这种模式还允许我们对接口而不是实现本身进行编程。

## 编码挑战:表情符号拾音器

[![emoji picker](img/1dd2e98488d158f8c056dad957724729.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZNrQAb-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hinsencamp.com/article/closures-vue/emoji-picker.png)

太好了，这就是我们在 VueJS 中实际构建强大的东西和利用闭包的力量所需要的所有理论！

事实上，高阶函数是最有趣的用例，因为我们在 VueJS 中已经有了数据隐私的概念。

基本上，组件已经通过属性和数据对象提供了一个不能从外部访问的接口。

这就是我们想要建立的！

[https://codesandbox.io/embed/842rp5j4n8](https://codesandbox.io/embed/842rp5j4n8)

它是的一个组件，允许用户在所有可用色调的基础上选择肤色，类似于在智能手机上发送短信的用户体验。

从技术上来说，你应该尝试创建一个组件，接收一个单一的表情符号作为道具，并使用高阶函数来创建多个点击事件处理程序，以选择不同的音调。

### [印度语](#hint)

表情符号可以存储为字符串值中的 HTML 十六进制代码。双手合十表情符号是🙏。要改变色调，请给它附加一个颜色代码。你可以在这里找到密码。

> 🙏 + 🏽 =🙏🏽

## 建筑挑战扩展

你想更进一步，看看你是否掌握了闭包？然后传递多个表情符号，让它发挥作用，这样你就可以一次改变多个表情符号的肤色。💯

## 结论

闭包是我们可以在相关函数可能已经终止的时候访问父作用域的变量的原因。

我们可以在 VueJS 中使用 JavaScript 的这种行为来基于动态参数动态构建方法，而不会因为大量的变量和方法而污染我们的组件。

感谢阅读🙌

最初发表于[hinsencamp.com](https://hinsencamp.com/article/closures-vue/)