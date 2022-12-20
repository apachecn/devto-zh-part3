# 当嵌套承诺是正确的

> 原文：<https://dev.to/glebec/when-nesting-promises-is-correct-h8f>

# [T1】简介](#intro)

到目前为止，[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)在 JS 生态系统中已经很完善了，不仅在 [ECMAScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 中被正式指定，甚至以 [`async`](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) 函数的形式拥有了一流的语法糖。

当学习承诺时，许多 JS 开发人员被告知承诺链的一个主要优点是它保持代码“扁平”，避免嵌套回调的[末日金字塔](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming))。虽然这在一定程度上是正确的，但它也过分强调了代码*外观*，冒着[错过要点](https://blog.domenic.me/youre-missing-the-point-of-promises/)的风险。

真正的[“回调地狱”](http://callbackhell.com/)与缩进关系不大——事实上，通过命名回调函数并将它们分解到顶层，人们通常可以简化异步代码*而不需要*承诺。相反，回调地狱是当我们失去可组合的普通函数 API(传入数据，接收结果)时，返回值可以绑定到变量，聚集在集合中，传递给其他函数，并以一流的方式组合。

所有这些序言都是为了给下面的陈述提供上下文:嵌套承诺通常是反模式，但 T2 并不总是反模式。事实上，有一种常见的情况，一点点嵌套可能是完全合理的，尽管存在几种替代方案。这篇短文将展示一个常见的范围界定问题，以及针对该问题的承诺和多种解决方案。

# 设置

对于这些例子，我们将假设函数`getPuppyById`是一个 AJAX 方法，通过一个承诺返回一些数据。小狗将成为另一只小狗的`bestFriend`外键的对象:

```
{
    id: 4,               // this puppy's id
    name: 'Mr. Wiggles', // this puppy's name
    bestFriend: 17       // id of this puppy's best friend (another puppy)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们希望获取小狗#1 最好朋友的名字，我们可以将调用链接到`getPuppyById`:

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const friendNameP = getPuppyById(1) // first fetch .then(pup1 => getPuppyById(pup1.bestFriend)) // second fetch .then(friend => friend.name) // data transformation friendNameP // our goal, a promise for the best friend name .then(name => console.log('friend name', name)) .catch(e => console.error(e))`

当我们的早期结果只是朝着我们想要的最终结果的可丢弃的步骤时，这就很好了。

# 问题

然而，如果我们想为两只小狗的名字——最初的和朋友的名字产生一个承诺呢？因为传递给`then`的回调引入了一个函数作用域，第一只小狗可能不再在链的下游。

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const twoPuppyNamesP = getPuppyById(1) // first fetch .then(pup1 => getPuppyById(pup1.bestFriend)) // second fetch .then(friend => { return [pup1.name, friend.name] // ERROR – pup1 no longer in scope! }) // DO NOT EDIT BELOW twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

有多种方法可以解决这个问题，我们稍后将对此进行研究。在我们这样做之前，使用你可能喜欢的任何技术*来修复上面的代码片段。仅编辑代码段的上半部分；你在努力让`twoPuppyNamesP`履行承诺(哈)给*两只*小狗接生。*

# 方案

## 图书馆专用:蓝鸟`bind`

在 ES2015 承诺成为官方之前，像[蓝鸟](http://bluebirdjs.com/docs/getting-started.html)这样的第三方实现很流行。蓝鸟仍然被一些代码库使用，因为它的速度和广泛的实用方法。

虽然这样做违反了 A+ promise 规范的[第 2.2.5 节，但是 Bluebird 包含了一个特殊的特性，可以在其中设置承诺链的`this`值——提供了一个共享的可变名称空间来保存中间结果。具体方法命名为](https://promisesaplus.com/#point-35) [`bind`](http://bluebirdjs.com/docs/api/promise.bind.html) 。

> *`.bind`还有一个有用的附带目的——许诺处理程序不需要共享一个函数来使用共享状态*

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const P = require('bluebird') const toBluebird = p => P.resolve(p) const twoPuppyNamesP = toBluebird(getPuppyById(1)) // first fetch .bind({}) // set 'this' for chain .then(function (pup1) { // arrows don't have 'this' this.pup1 = pup1 // saving state for later return getPuppyById(pup1.bestFriend) // second fetch }) .then(function (friend) { return [this.pup1.name, friend.name] // accessing 'pup1' in shared state }) twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

虽然这个*可以工作*，但是它有很大的缺点:

*   它通过违反规范的特性使承诺链变得复杂
*   需要使用`function`函数来访问`this`
*   它是绑定到特定库的不可移植的知识

## 符合 A+，ECMA 批准:`Promise.all`

如果我们能够通过一个承诺链向下传递多个值就好了——即使其中一个值是待定的承诺，我们也希望进一步访问它的值。

当然，我们不需要希望这样的特性，因为它可以通过 [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 静态方法获得。通过返回由*同步值*和*承诺值*组成的数组，包装在对`all`的调用中，我们可以在下一个`then`中访问由*同步值*组成的数组。

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const twoPuppyNamesP = getPuppyById(1) // first fetch .then(pup1 => { const friendP = getPuppyById(pup1.bestFriend) // second fetch return Promise.all([pup1, friendP]) // collect both results }) .then(([pup1, friend]) => { // array destructuring return [pup1.name, friend.name] // data transformation }) twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

尽管传递给`.all`的数组混合了正常值和承诺值，但最终得到的总承诺值是一个正常值数组。

这种策略将在任何支持 ES2015 的设置中工作，因此比蓝鸟`bind`技巧更易于移植。不幸的是，它也有缺点:

*   更详细的返回行
*   更复杂的函数参数和重构
*   随着链的增长，传递多个结果并不能很好地扩展
*   总的来说，早期价值链中有许多多余的“管道”

## 受控状态，共享范围

我们现在来看一种通过承诺链共享状态的最常见和可行的技术——在更高的范围内使用可变或可重新赋值的变量。当调用`then`链中的每个处理程序时，它将设置和/或读取共享`let`绑定的值或共享对象的属性。

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `let pup1 // shared binding const twoPuppyNamesP = getPuppyById(1) // first fetch .then(gotPup1 => { pup1 = gotPup1 // save state return getPuppyById(pup1.bestFriend) // second fetch }) .then(friend => { return [pup1.name, friend.name] // data transformation }) twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

考虑到我们通常认为异步代码是如何工作的，这看起来似乎是“非法的”,但事实上它被保证按预期工作，因为在一个`then`链中后面的回调只能在前面的回调之后被调用。所以在第二个`then`中使用`pup1`将会起作用，因为`pup1`肯定已经在前一个`then`的回调中被赋值了。

这有一些明显的优势:

*   这一点甚至对那些没有提前了解承诺的人来说也是相对清楚的
*   它与环境无关
*   它在语法上相对较轻
*   链条保持平直，减轻了精神负担

然而，和往常一样，仍然需要考虑权衡。

*   共享可变状态有风险；应该注意的是*只有*允许承诺链读取或修改这些变量
    *   由于不确定的计时，链外读取不能保证工作
    *   写外链会破坏链内的保证
*   我们现在需要变量名的两个版本——像`gotPup1`这样的参数名和像`pup1`这样的共享状态变量——以避免[遮蔽](https://en.wikipedia.org/wiki/Variable_shadowing)

如果 promise 链本身包含在一个短的函数作用域内，那么在本地设置中有规律地使用共享状态可以是解决沿链传递信息问题的简洁而简单的方法。

## 笑点:嵌套承诺

本文开篇承诺(哈哈)展示一种情况，在这种情况下，一点点嵌套可能是一种有效和有用的技术。关键的一点是，有了嵌套链，内部的`then`仍然可以从外部的`then`访问结果。

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const twoPuppyNamesP = getPuppyById(1) // first fetch .then(pup1 => getPuppyById(pup1.bestFriend) // second fetch .then(friend => [pup1.name, friend.name]) // nested then ) twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

在这种情况下，**记住`return`** 父承诺链的嵌套承诺链是至关重要的。在上面的例子中，我们使用了一个 arrow 函数的隐式返回来完成这个任务，但是在一个用括号括起来的函数体中忘记关键字`return`是一个常见的错误。

与外部作用域变量相比，上述模式的最大优势在于它是无状态的——在可见代码中没有显式的变化，只有一个声明性的函数转换序列。

一如既往，我们可以发现一些缺点:

*   这种方法不能很好地扩展传递来自许多`then`调用的每个结果——对于这种情况，很快就会回到“末日金字塔”
*   嵌套增加了解析和理解承诺链逻辑的精神负担
*   正如承诺链经常出现的情况一样，就`.then`出现的位置(同一行)而言，决定一个合理的格式化方案可能特别困难。下一行？缩进？)以及回调函数的位置

## 愚蠢的实验:格式化的把戏

说到格式化，如果我们允许堆积括号，没有理由不能以“平面”方式格式化嵌套的承诺链:

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const twoPuppyNamesP = getPuppyById(1) // first fetch .then(pup1 => getPuppyById(pup1.bestFriend) // second fetch (missing closing paren) .then(friend => [pup1.name, friend.name])) // nested then (extra closing paren) twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

嵌套的链越长，我们就越推迟到最后一行结束，在最后一行，它们会像事后想法一样堆积起来。在像 Haskell 这样的语言中，函数应用程序*不使用*parens，这不是问题！但是对于 JavaScript 来说，这有点傻。比较和对比:

```
-- Haskell

_then = (>>=) -- renaming for JS readers; can't use 'then' b/c it's a keyword

pupsIO =
    getPuppyById 1
    `_then` \pup1 -> getPuppyById (bestFriend pup1)
    `_then` \pup2 -> getPuppyById (bestFriend pup2)
    `_then` \pup3 -> getPuppyById (bestFriend pup3)
    `_then` \pup4 -> getPuppyById (bestFriend pup4)
    `_then` \pup5 -> pure [pup1, pup2, pup3, pup4, pup5] 
```

Enter fullscreen mode Exit fullscreen mode

```
// JavaScript

const pupsP =
    getPuppyById(1)
    .then(pup1 => getPuppyById(pup1.bestFriend)
    .then(pup2 => getPuppyById(pup2.bestFriend)
    .then(pup3 => getPuppyById(pup3.bestFriend)
    .then(pup4 => getPuppyById(pup4.bestFriend)
    .then(pup5 => [pup1, pup2, pup3, pup4, pup5]))))) // lol 
```

Enter fullscreen mode Exit fullscreen mode

## 应许之地:异步/等待

越过承诺链的困境，我们回到手头的实际问题——承诺链由回调函数组成，函数在语法上引入了新的作用域。如果我们没有兄弟作用域，我们可以共享对以前结果的访问。

瞧，这是`async`函数解决的问题之一。

`const getPuppyById = (id) => new Promise((res) => { const time = Math.random() * 500 + 500 setTimeout(() => res({ 1: { name: 'Floof', id: 1, bestFriend: 7 }, 7: { name: 'Rex', id: 7, bestFriend: 1 } }[id]), time) })` `const getTwoPuppyNamesP = async () => { // a shared async function scope const pup1 = await getPuppyById(1) // first fetch const friend = await getPuppyById(pup1.bestFriend) // second fetch return [pup1.name, friend.name] // data transformation } const twoPuppyNamesP = getTwoPuppyNamesP() // async funcs return promises twoPuppyNamesP // our goal, a promise for the puppy and friend names .then(names => console.log('puppy names', names)) .catch(e => console.error(e))`

优势是显著的:

*   噪音小得多(没有`.then`调用或回调函数)
*   具有同步外观的代码，可以访问范围内以前的结果

成本非常低:

*   `await`关键字只能在`async`函数中使用，所以我们需要将我们的承诺代码包装在函数体中

Async/await 类似于 Haskell 的`do`-符号，其中`do`类似于`async`，`<-`类似于`await` :

```
-- Haskell

twoPuppyNames = do
    pup1   <- getPuppyById 1
    friend <- getPuppyById (bestFriend pup1)
    pure [name pup1, name friend] 
```

Enter fullscreen mode Exit fullscreen mode

一个主要的区别是 JS 中的`async` / `await`仅用于承诺，而 Haskell 的`do`符号适用于任何[单子](https://wiki.haskell.org/Monad)。

# 结论

随着 async/await 的出现，程序员越来越少使用原始承诺链。Async/await 有自己需要掌握的微妙之处，但它巧妙地解决了承诺链的至少一个棘手方面，即在一系列操作中访问以前的异步结果。

正如本文的标题所暗示的，在编写手动承诺链时，使用一点局部嵌套有时是完全有效的。这样做可以在范围内保留多个结果，而不需要特殊的库技巧或有状态赋值。

无论如何，我希望这些例子能帮助学习 JS promises 的人更好地理解它们，更自信地使用它们。