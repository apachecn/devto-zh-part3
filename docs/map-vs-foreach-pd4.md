# 。map()与 forEach()

> 原文：<https://dev.to/torianne02/map-vs-foreach-pd4>

我快速解释了使用`.map()`和`.forEach()`的区别，因为我最初很难理解为什么你想使用其中一个而不是另一个的根本原因。我以为你可以互换使用它们，这没什么大不了的，但我错了。

我个人知道，在学习一门新语言的同时，总是这些小细节真正让你陷入困境，所以我想尽可能快速简单地做出这种解释，以便于理解！

让我们开始吧。

### `.forEach()`

`.forEach()`是一个数组迭代器，它对给定数组中的每个元素执行一次函数(由开发者提供)。这意味着，作为开发人员，我们需要提供一个函数，我们希望在数组的每个元素上使用这个函数。假设我们有一组狗，我们希望每只狗都能得到一份礼物。我们可以通过执行下面的代码来做到这一点:

```
var dogs = [“Spot”, “Buddy”, “Princess”]

dogs.forEach(function(dog) {
  console.log(dog + “ has eaten the treat!”)
});

// “Spot has eaten the treat!”
// “Buddy has eaten the treat!”
// “Princess has eaten the treat!”

console.log(dogs) // [“Spot”, “Buddy”, “Princess”] 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们有了数组的每个元素加上语句“已经吃了零食！”打印到控制台。此时我必须注意，虽然它将这些语句打印到控制台，但是函数中正在执行的内容并没有保存在任何地方。原始数组没有被操作，也没有创建新数组。这就是为什么当我调用`dogs`时，它会完整无损地返回原始数组！

如果您想保存函数的结果，那么您需要手动保存。我可以通过简单地创建一个新的数组，并在函数中推送到这个新的数组来保存所有这些语句，就像这样:

```
var dogs = [“Spot”, “Buddy”, “Princess”]
var result = []

dogs.forEach(function(dog) {
  result.push(dog + “ has eaten the treat!”)
});

console.log(result) // [“Spot has eaten the treat!”, “Buddy has eaten the treat!”, “Princess has eaten the treat!”] 
```

Enter fullscreen mode Exit fullscreen mode

### `.map()`

`.map()`类似于`.forEach()`,因为它们都是数组迭代器，对给定数组中的每个元素执行一个函数。现在，两者之间的最大区别是，使用`.map()`，我们不需要像使用`.forEach()`那样告诉我们的函数将每个元素添加到一个新数组中。使用`.map()`,它从给定函数的结果中创建一个新的数组，而不会破坏原来的数组。换句话说，`.map()`允许我们转换数组中的元素，但是为了保存这个结果，我们仍然需要将`.map()`语句设置为一个新变量。让我们再次以我们的狗为例，我们将让每只狗玩捡球游戏。

```
var dogs = [“Spot”, “Buddy”, “Princess”]

var result = dogs.map(dog => dog + “ has fetched the ball!”);

console.log(result)  // [“Spot has fetched the ball!”, “Buddy has fetched the ball!”, “Princess has fetched the ball!”]
console.log(dogs) // [“Spot”, “Buddy”, “Princess”] 
```

Enter fullscreen mode Exit fullscreen mode

注意`result`现在是一个字符串数组，而`dogs`仍然保持原来的状态。

#### 最后的想法

如果你想迭代一个数组而不需要保存函数的结果，那么我建议使用`.forEach()`。与此相反，如果您需要函数的结果来执行依赖于它的其他代码，请使用`.map()`。

#### 来源

[array . prototype . map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
[array . prototype . forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
[为什么以及何时在 JavaScript 中使用 forEach、map、filter、reduce 和 find](https://medium.com/@JeffLombardJr/understanding-foreach-map-filter-and-find-in-javascript-f91da93b9f2c)