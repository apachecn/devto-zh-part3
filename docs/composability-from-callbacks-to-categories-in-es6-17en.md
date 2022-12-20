# 可组合性:从回调到 ES6 中的类别

> 原文：<https://dev.to/homam/composability-from-callbacks-to-categories-in-es6-17en>

承诺是异步 JavaScript 程序中出现的回调地狱问题的一个众所周知的解决方案。

在这篇文章中，我借用了函数式语言的一些思想，探索一种不同的方法来解决回调问题。这个解决方案将比承诺更通用，事实上我们将利用这些想法使承诺更加可组合。

我使用类似 Haskell 的符号。但是在许多方面，我会在任何我认为有帮助的地方偏离严格的 Haskell 符号。

你只需要熟悉回调、承诺和 ES6 匿名函数(lambda)语法就能理解这篇文章。我们将使用函数式编程(FP)中的一些想法。

目录:

*   [回调](#callbacks)
*   [可组合回调类](#composable-callback)
*   [可组合回调类和承诺类是单子](#callback-and-promise-are-monads)
*   [单子](#what-is-a-monad)
*   [类别](#categories)
*   [功能类别](#func-category)
*   [克莱斯利类别](#functions-that-return-a-monad-form-a-category)

## 回调

许多编程语言利用回调来延续。当我们遇到:

```
 db.getSomething(callback) 
```

我们知道`db.getSomething`是一个`void`函数，它执行一些代码(可能是异步的)并将操作结果传递给`callback`函数来处理它。

JavaScript 中的回调比延续更强大。我们可以使用回调函数对返回多个结果的函数建模:

```
function next2(x, callback) {
  callback(x + 1, x + 2)
}

next2(10, (eleven, twelve) => …) 
```

事实上，回调就是这样被用来传播错误的。按照惯例，回调的第一个参数是操作产生的错误(如果有):

```
function sqrt(x, callback) { 
  if(x < 0) 
    callback(Error('Sqrt of negative value', null))
  else 
    callback(null, Math.sqrt(x))
} 
```

如果操作产生任何错误，我们总是忽略第二个参数(不管它可能产生什么结果)。

当我们想要将第一个异步操作的结果传递给第二个异步函数和第三个异步函数时，回调地狱就会发生:

```
function myLongOperation(userId, callback) {
  db.getUser(userId, (error, user) => {
    if(!!error)
      return callback(error, null)
    else
      api.generateMessage(user, (error, message) => { 
          if(!!error)
            return callback(error, null) 
          else
            client.sendMessage(message, callback)
      })
  })
} 
```

在这里，我们将`userId`传递给`getUser`，以便异步获取`user`，然后我们将`user`传递给`generateMessage`,而不是用文字来描述，让我们用一些符号来描述这个过程:

> userid→getuser 生成消息 sendMessage

上面的符号完美地描述了我们的`myLongOperation`函数的功能。每一步的错误处理显然是多余的。Promise 迷们知道这种符号与我们对待承诺的方式非常相似(但不完全相同):

```
 getUser(userId).then(generateMessage).then(sendMessage) 
```

负责错误处理和链接。

但是我们的目标是提出一个比承诺更普遍的概念。

在我们的符号中`⋙`是一种组合方式(管道异步函数)。我们以后再讨论。

`x → y`表示从`x`到`y`的功能。例如:

```
const plus1 = x => x + 1
//        Number → Number 
```

`myLongOperation`是从`userId`到一系列异步操作的函数，因此:

```
 userId → ( … ⋙ … ⋙ … ) 
```

Haskellers 知道这不是一个合适的类型定义。但是为了我们的目的，这个符号完美地描述了`myLongOperation`函数。

### 可组合回调

承诺不是解决回调地狱问题的唯一办法。承诺提供了比可组合性更多的特性(例如，它们有一个内部状态，可以记住它们是否已经被解决，以及其他一些问题)。

让我们通过实现一个“可组合回调”类来定义回调地狱问题的最小解决方案:

```
 class Callback {
  constructor(f) {

    // this.run = f
    this.run = callback => {
      try {
        f(callback)
      } catch (ex) {
        callback(ex, null)
      }
    }

    // this.map = ...
    // this.bind = ...

    // this :: Callback x
    // x -> (y || Callback y) -> Callback y
    this.then = g => new Callback(callback => {
      this.run((error, ...result) => {
        if(!!error) {
          callback(error, null)
        } else {
          try {
            const y = g(...result)
            if (y instanceof Callback) {
              y.run(callback)
            } else {
              callback(null, y)
            }
          } catch(ex) {
            callback(ex, null) 
          }
        }
      })
    })

    this.bindTo = g => this.bind(Callback.from(g))
  }
}

// x -> Callback x
Callback.pure = x => new Callback(cb => cb(null, x))

Callback.resolve = Callback.pure

// Callback.from casts f into a Callback instance, where
// f is a function that takes x and a callback function
Callback.from = f => (...x) => new Callback(cb => f(...x, cb)) 
```

点击查看[的完整代码。](https://gist.github.com/homam/5d583f0fcd04a0a0ccbdf2ba0eb861c3#file-callback-monad-js)

回调类提供了这个接口:

*   ***构造函数*** 采用一个异步函数(`f`，它将产生一个错误或一个值`x`

*   ***运行*** 实例函数:接收回调函数并反馈给`f`

*   ***映射*** 实例函数类似于 Array.map，将`x`(`f`的结果)进行变换

*   ***绑定*** 实例的功能与`Promise.then`类似，用于链接回调实例

*   ***则*** 实例功能对应`Promise.then`；它是`map`和`bind`功能的组合。

*   ***bindTo*** 实例函数是一个将回调实例链接到普通异步函数的实用程序

*   ***纯*** (别名`resolve`)静态函数类似于`Promise.resolve`，它创建一个回调的实例。

*   *静态函数将异步函数强制转换为回调的实例。*

 *回调接口酷似无极的接口并不是偶然。`pure`是`resolve`的别名。如果你用过`Promise.resolve()`，你就知道`Callback.pure`是干什么的。我认为`pure`是我们回拨班更好的名字。类似地`Callback.then`类似于`Promise.then`。我有意识的避开`Callback.map`和回调。`bind`。函数，因为* Callback.then *足够了，因为它既映射又绑定。

我们从`Callback.pure`开始。它将一个值放入一个新的回调实例:

```
 Callback.pure(64).run((error, result) => console.log(result)) 
```

将在控制台中记录 64。

这就是我们如何创作`Callback.pure(64)`。使用我们的`sqrt`功能:

[https://repl.it/@homami/Callback?lite=true](https://repl.it/@homami/Callback?lite=true)

```
 Callback.pure(64)
    .bindTo(sqrt)
  .run((error, result) => console.log(error || result)) 
```

在幕后，`bindTo`将`sqrt`转换为回调的一个实例。上面的代码片段相当于下面的:

```
Callback.pure(64)
  .then(Callback.from(sqrt))
.run((error, result) => console.log(error || result))

Callback.pure(64)
  .then(x => new Callback(cb => sqrt(x, cb)))
.run((error, result) => console.log(error || result)) 
```

使用回调类，我们的`myLongOperation`函数可以更简洁地写成:

```
 // userId → (getUser ⋙ genMessage ⋙ sendMessage)

    const myLongOperation = (userId, callback) => 
      Callback.pure(userId)
        .bindTo(getUser).bindTo(genMesssage).bindTo(sendMessage)
      .run(callback) 
```

请注意这个实现与符号的匹配程度。

*   `.bindTo(getUser).bindTo(genMesssage).bindTo(sendMessage)`。由`(getUser ⋙ genMessage ⋙ sendMessage)`表示

*   但是`Callback.pure(userId)`似乎没有必要。(`userId → (…)`是整个 myLongOperation 函数的外延。)我们稍后将回到这一点。

我们对`myLongOperation`功能的更改对此功能的用户不可见。`myLongOperation`仍然是一个异步函数，接受一个`userId`和一个`callback`。

我们总是可以使用`bindTo`工具将回调实例链接到异步函数。例如，假设我们有另一个类似于`getUserId(userName, callback)`的异步函数，我们希望将它的结果通过管道传输到`myLongOperation:`T3

```
const messageUser = (userName, callback) =>
  Callback.pure(userName)
  .bindTo(getUserId)
  .bindTo(myLongOperation)
  .run(callback) 
```

注意现在`run()`被调用了两次:一次在`myLongOperation`内部，第二次在`messageUser`内部。这里有一个陷阱。除非我们调用`run()`，否则什么都不会发生。

```
const proc = Callback.pure(5)
  .then(x => new Callback(cb => {
    console.log(`binding ${x} to x + 1`)
    setTimeout(() => cb(null, x + 1), 100)
  })) 
```

第三行中的`console.log()`只在`proc.run()`之后被调用。在此尝试:

[https://repl.it/@homami/Callback-1?lite=true](https://repl.it/@homami/Callback-1?lite=true)

`proc`(作为`Callback`类的实例)表示 JavaScript 仅在`run()`被调用后执行的异步操作的指令。这和承诺很不一样:

```
const prom = new Promise(resolve => {
  console.log('Promise executes immediately')
  resolve()
}) 
```

当您运行这个代码片段时，即使您从未使用过`prom`或`prom.then(x => …)`，也会立即记录“Promise executes immediately”。

所以让我们修改我们的`myLongOperation`函数来返回一个回调实例(我们可以这样保存对`run()`的一个调用):

```
// userId → (getUser ⋙ genMessage ⋙ sendMessage)

const myLongOperation = userId => 
  Callback.pure(userId)
  .bindTo(getUser).bindTo(genMesssage).bindTo(sendMessage) 
```

现在这个定义与符号更加匹配，因为我们完全消除了回调函数。

本着同样的精神，我们更新我们的`messageUser`函数:

```
// userName → (getUserId ⋙ myLongOperation)

const messageUser = userName =>
  Callback.pure(userName).bindTo(getUserId).then(myLongOperation) 
```

我们改了最后一个`bindTo()`。到`then()`，因为现在我们更新后的`myLongOperation`是一个返回`Callback`实例的函数(记得最初在改变之前，它是一个在第二个参数中接受回调的 void 函数)。

这就是我们如何使用`messageUser` :

```
messageUser(userName).run((error, result) => ...) 
```

我们只在操作结束时调用`run()`。`run()`执行操作并在其回调参数中返回结果。

我们实现了可组合性，避免了回调地狱，而没有诉诸承诺。点击此处查看完整示例:

[https://repl.it/@homami/Callback-3?lite=true](https://repl.it/@homami/Callback-3?lite=true)

函数式程序员知道必须有一些 [eta 减少](https://wiki.haskell.org/Eta_conversion)来转换

`myLongOperation(userId) = userId → (getUser ⋙ genMessage ⋙ sendMessage)`到
再到`myLongOperation = getUser ⋙ genMessage ⋙ sendMessage`

在这篇文章的其余部分，我们构建一些结构，最终使我们能够消除这个多余的参数。

### 回调和许诺都是单子

我们的回调类和标准的 Promise 类有很多共同之处。我们称这些构造为 monad，我的意思是它们有一个`bind` ( `then`)函数，将回调(或承诺)的一个实例链接到返回回调(或承诺)的另一个实例的函数。

```
 const proc = Callback.pure(10)
    proc.bind(x => new Callback(…)) 
```

我们使用这种符号将`proc`描述为回调单子的一个实例:

> 过程::回调 x
> 
> proc.bind :: (x →回调 y) →回调 y

我们可以这样读符号:

*   `proc`是`x`的回调

*   `proc.bind`是一个(高阶)函数，它从`x`到`y`的回调之间取一个函数，并产生一个`y`的回调。

例如，`Callback.pure(10)`可以绑定到一个函数，该函数接受一个数字并返回一个新的回调:

```
Callback.pure(10)
  .bind(x => new Callback(cb => cb(null, x + 1))) 
```

(请记住，`resolve()`是`pure()`的别名，`then()`与`bind()`的功能相似)

无极类也形成单子:

```
Promise.resolve(10)
  .then(x => new Promise(resolve => resolve(x + 1))) 
```

这两个表达式看起来非常相似，这就是单子的力量。单子提供了在许多不同程序中有用的抽象。在我们的符号中，上述表达式可以写成:

> 单子 10 ≫= (x →单子(x + 1)) =单子 11

对于诺言单子:

```
 Monad 10           ::  Promise.resolve(10)
    ≫=                 ::  .then(…)    
    x → Monad (x + 1)  ::  x => new Promise(resolve => resolve(x + 1)) 
```

对于回调单子:

```
 Monad 10           ::  Callback.resolve(10) // = Callback.pure(10)
    ≫=                 ::  .then(…)             // = Callback.bind(…)
    x → Monad (x + 1)  ::  x => new Callback(cb => cb(x + 1)) 
```

单子封装了一个只能通过执行单子来检索的值。对于承诺单子，我们通过调用`then()`函数检索计算结果(11 ),对于回调单子，我们通过`run()`检索结果。

单子有一个有趣的特性，即使它们的封装值还没有计算出来，也可以使用。我们能够在一个承诺上调用`then()`,并用一个函数或另一个承诺将它链接起来，即使它还没有完成，它封装的值还没有计算出来。这个事实对于我们的回调单子来说更加明显。我们之前已经看到，在我们调用`run()` ( [Repl.it demo](https://repl.it/@homami/Callback-2) )之前，**回调甚至都懒得开始计算它的结果。**

更一般地，两种计算都可以表示为:

> 单子 x ≫= (x →单子 y) =单子 y

`x`和`y`可以是任意类型。这里它们是数字，但是它们可以是字符串、布尔、JSON 对象……甚至是函数或其他单子！

### 什么是单子？

为了我们的目的，任何具有这两个特征的`class`都是单子:

*   该类必须有封装值的方法(使用静态的`pure()`或`resolve()`函数)

*   它必须提供一种方法将自己与返回它的另一个实例的函数绑定(使用`bind()`或`then()`)

单子给它们封装的值增加了额外的结构。不同类型的单子提供不同的结构。`pure`函数的实现就是寻找这些结构的地方。

为了承诺:

```
 Promise.resolve = x => new Promise(res => res(x)) 
```

对于[回拨](https://jsbin.com/xiwano/edit?js,console) :

```
 Callback.pure = x => new Callback(cb => cb(null, x)) 
```

对于[阵](https://jsbin.com/tebogiq/edit?js,console) :

```
 Array.of = x => [x] 
```

给[读者](https://jsbin.com/tenupa/edit?js,console) :

```
 Reader.pure = x => new Reader(env => x) 
```

点击链接查看定义并玩这些单子。在这篇文章中，我们只研究承诺和回拨。

我们确实可以定义一个几乎没有额外结构的单子。这个最小单子叫做[恒等式](https://jsbin.com/gekuneq/edit?js,console)单子:

```
 Identity.pure = x => new Identity(x) 
```

身份如何有用可以是另一篇文章的主题。

## 类别

函数式编程关注的是**什么**而不是**如何**。我们通过声明我们想要什么来编写程序，而不是一步一步地实现程序，详细说明程序是如何工作的。

例如在这段代码中:

```
 const myLongOperation = userId => 
      Callback.pure(userId)
      .bindTo(getUser).bindTo(genMesssage).bindTo(sendMessage)

    myLongOperation(123456).run((error, result) => ...) 
```

当我们调用`run()`时，我们知道其中涉及回调和错误处理。但我们看不到它，也不必在意这些细节。相反，在这里我们通过描述我们想要的来编写我们的程序:

*   获取一个*用户*

*   为用户生成消息

**   发送*该消息*(并异步返回*SendMessageResult* )* 

 *`myLongOperation`是 SendMessageResult 从`userId`到*的回调函数。*

> myLongOperation :: userId ->回调 SendMessageResult。

一元抽象关注操作的结果。例如*回调 SendMessageResult* 只告诉我们动作的结果(即 *SendMessageResult* )而不是它来自哪里。单子不处理输入。它们只是定义了一种使用 bind (then)合成输出的方法。

现在让我们试着创建一个同时考虑输入和输出的抽象。

### 良好的旧功能

有输入和输出的最简单的构造是一个简单的函数。

```
 const plus1  = x => x + 1
    const times2 = x => x * 2 
```

我们可以使用函数组合来组合函数，用数学符号表示:

> f o g = x → f(g(x))对

用 JavaScript ( [演示](https://ramdajs.com/repl/#?const%20plus1%20%20%3D%20x%20%3D%3E%20x%20%2B%201%0Aconst%20times2%20%3D%20x%20%3D%3E%20x%20%2A%202%0Aconst%20id%20%3D%20x%20%3D%3E%20x%0A%0Aconst%20times2Plus1%20%3D%20compose%28plus1%2C%20times2%29%0A%0Aconsole.log%28%60%28plus1%20o%20times2%29%2810%29%20%3D%20%24%7B%20times2Plus1%2810%29%20%7D%60%29) ):

```
 const compose = (f, g) => x => f(g(x)) 
```

函数组合是从右向左的操作。 *compose(f，g)(x)* ，先将`g`应用于`x`，再将`f`应用于 *g(x)，因此* :

```
 compose(plus1, times2)(10) == 21 
```

但是这里我更喜欢使用管道操作符从左到右组合:

> f≤g = x→g(f(x)]

```
 const pipe = (f, g) => x => g(f(x))

    pipe(plus1, times2)(10) // == 22 
```

[函数组合通常是不可交换的](https://ramdajs.com/repl/#?const%20plus1%20%20%3D%20x%20%3D%3E%20x%20%2B%201%0Aconst%20times2%20%3D%20x%20%3D%3E%20x%20%2A%202%0Aconst%20id%20%3D%20x%20%3D%3E%20x%0A%0Aconst%20plus1Times2%20%3D%20pipe%28plus1%2C%20times2%29%0Aconst%20times2Plus1%20%3D%20pipe%28times2%2C%20plus1%29%0A%0Aconsole.log%28%60%28plus1%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7B%20plus1Times2%2810%29%20%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20plus1%29%2810%29%20%3D%20%24%7B%20times2Plus1%2810%29%7D%20%60%29%0A)我的意思是:

> f g g g f

我们已经看到([片段](https://ramdajs.com/repl/#?const%20plus1%20%20%3D%20x%20%3D%3E%20x%20%2B%201%0Aconst%20times2%20%3D%20x%20%3D%3E%20x%20%2A%202%0Aconst%20id%20%3D%20x%20%3D%3E%20x%0A%0Aconst%20plust1Times2%20%3D%20pipe%28plus1%2C%20times2%29%0Aconst%20times2Plus1%20%3D%20pipe%28times2%2C%20plus1%29%0A%0Aconsole.log%28%60%28plus1%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7B%20plust1Times2%2810%29%20%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20plus1%29%2810%29%20%3D%20%24%7B%20times2Plus1%2810%29%7D%20%60%29%0A) ):

```
 pipe(plus1, times2)(10) != pipe(times2, plus1)(10) 
```

但是有一个特殊的函数，它的函数合成总是可交换的，我们把这个函数命名为`id`:

> ⋙ id = id ⋙ f

我们将其定义为

```
 const id = x => x 
```

简单耶！

[我们来试试](https://ramdajs.com/repl/#?const%20plus1%20%20%3D%20x%20%3D%3E%20x%20%2B%201%0Aconst%20times2%20%3D%20x%20%3D%3E%20x%20%2A%202%0Aconst%20id%20%3D%20x%20%3D%3E%20x%0A%0Aconsole.log%28%60%28plus1%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7Bpipe%28plus1%2C%20times2%29%2810%29%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20plus1%29%2810%29%20%3D%20%24%7Bpipe%28times2%2C%20plus1%29%2810%29%7D%60%29%0A%0Aconsole.log%28%27------%27%29%0A%0Aconsole.log%28%60%28id%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7Bpipe%28id%2C%20times2%29%2810%29%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20id%29%2810%29%20%3D%20%24%7Bpipe%28times2%2C%20id%29%2810%29%7D%60%29%0A)(片段):

```
 pipe(times2, id)(10) // == 20
    pipe(id, times2)(10) // == 20 
```

与函数类似，还有其他具有这两种属性的构造:

*   它们是可组合的

*   它们有一个特殊的 id 实例，其组合是可交换的

我们称这些结构为范畴。

### Func 类别

我们来做一个正常函数的 Category 类:

```
class Func {
  constructor(f) {
    // this.run = f
    this.run = x => f(x)

    // this :: Cat (x ↣ y)
    // Cat (y ↣ z) -> Cat (x ↣ z)
    this.pipe = g => new Func(x => g.run(this.run(x)))

    // utility function that pipes Func to a normal function
    // this :: Cat (x ↣ y)
    // (y -> z) -> Cat (x ↣ z)
    this.pipeTo = g => new Func(x => g(this.run(x)))
  }
}
// Cat (x ↣ x)
Func.id = new Func(x => x) 
```

我用时髦的阿罗·↣来强调，类别抽象出一个有输入和输出的结构。

`Func.id`在`Func.pipe()` :
上确实是可换的

```
 Func.id.pipe(new Func(x => x * 2)).run(10) // = 20
    new Func(x => x * 2).pipe(Func.id).run(10) // = 20 
```

[https://repl.it/@homami/Callback-2?lite=true](https://repl.it/@homami/Callback-2?lite=true)

注意这里有且只有一个`Func.id`的实例。`Func.id`不是一个函数，它是`Func`类的一个实例。

Func 可能看起来很无聊，因为它只包装普通的函数:

```
 new Func(x => x * 2).run(5) == (x => x * 2)(5) 
```

但是 Func 使我们能够在 JavaScript([JSBin demo](https://jsbin.com/kecoxa/edit?js,console)):
中以一种自然的方式传输(组合)函数

```
 new Func(x => x * 2)
      .pipe(new Func(x => x + 1))
      .pipe(new Func(x => Math.sqrt(x)))
    .run(12)  // == 5 
```

让我们将上面的代码片段与 Promise monad 的类似代码进行比较:

```
 Callback.pure(12)
      .then(x => Promise.resolve(x * 2))
      .then(x => Promise.resolve(x + 1))
      .then(x => Promise.resolve(Math.sqrt(x)))
    .run((error, result) => console.log(result) /* result == 5 */) 
```

这两者之间有一些结构上的差异:

使用分类操作，我们已经能够在末尾(使用`run(12)`)输入，但是使用一元操作，我们必须在开始时通过`Callback.pure`输入。

其次，一元绑定具有以下形式:

> (单子 x) ≫= (x →单子 y) =单子 y
> 
> 。bind(x = >新回调(cb => cb(null，x + 1)))

但是绝对的`pipe`具有以下形式:

> 猫(x ↣ y) ⋙猫(y ↣ z) =猫(x ↣ z)
> 
> 。管道(新函数(x => x + 1))

很明显，范畴用输入和输出来抽象结构。这里的`Func`是从`x`到`x + 1`的一个函数的抽象。

### 返回单子的函数形成一个类别

我们看到正规函数`(x → y)`形成了一个我们称之为 Func 的范畴。一元绑定的右边是一个函数，它接受一个`x`并返回一个`y` : `(x → Monad y)`的一元。这些功能也形成了一个重要的类别，叫做[克莱里类别](https://en.wikipedia.org/wiki/Kleisli_category) :

```
class Kleisli {

  // given f :: x -> Monad y, constructs a category of type:
  // Cat (x ↣ y)
  constructor(f) {

    // this.run = f
    this.run = x => f(x)

    // this :: Cat (x ↣ y)
    // Cat (y ↣ z) -> Cat (x ↣ z)
    this.pipe = g => new Kleisli(x => this.run(x).then(g.run)) // then == bind

    // utility functon:
    // this :: Cat (x ↣ y)
    // (y -> Monad z) -> Cat (x ↣ z)
    this.pipeTo = g => new Kleisli(x => this.run(x).then(g)) // then == bind
  }
}

// Monad => Cat (x ↣ x)
Kleisli.id = monad => new Kleisli(x => monad.resolve(x)) 
```

我们可以使用 Kleisli 类别来表示承诺单子，比如( [JSBin](https://jsbin.com/hokimi/edit?js,console) ):

```
const times2Plus1 = new Kleisli(x => Promise.resolve(x * 2))
  .pipeTo(x => Promise.resolve(x + 1))

times2Plus1.run(10)
.then(x => console.log(x)) // == 21
.catch(error => console.error(error)) 
```

调用`times2Plus1.run(10)`的结果是一个承诺，我们用它通常的`then`和`catch`方法消费了这个承诺。

`pipeTo()`是一个实用函数，它将一个`Kleisli (x ↣ y)`通过管道传输到一个来自`( y → Monad z)`的普通函数，并产生一个新的`Kleisli (x ↣ z)`

> 克莱斯利(x ↣单子叶 y)。pipe to(y→monad z)= kle isli(x↣monad z)
> 
> klesli(x≤monad y).管道(klesli(y = monad z))= klesli(x = monad z)

如果没有`pipeTo`，我们可以通过`new` :
手动将`(y → Monad z)`铸造成`Kleisli (y → Monad z)`

```
const times2Plus1 = new Kleisli(x => Promise.resolve(x * 2))
  .pipe(new Kleisli(x => Promise.resolve(x + 1))) 
```

Kleisli 范畴的妙处在于，它是为任何类型的单子定义的。这里有一个 Promise monad 的例子:( [JSBin](https://jsbin.com/wuduri/edit?js,console) )。

另一个演示展示了相同的 Kleisli 类对 Promise 和 Callback monad 都有效:

[https://repl.it/@homami/Callback-4?lite=true](https://repl.it/@homami/Callback-4?lite=true)

使用 Kleisli 类别我们的`myLongOperation`可以实现为:

```
// myLongOperation :: Category (userId ↣ Promise SendMessageResult)

const myLongOperation = new Kleisli(getUser)
  .pipeTo(genMesssage)
  .pipeTo(sendMessage)

myLongOperation.run(123456).then(sendMessageResult => …) 
```

我们通过调用`then(result => )`来检索最终结果，因为我们的 Kleisli 类别中的底层单子是承诺单子，我们假设`getUser`、`genMessage`和`sendMessage`是返回承诺的函数:

```
 getUser     = userId => new Promise(resolve => … resolve(user))
    genMessage  = user   => new Promise(resolve => … resolve(msg))
    sendMessage = msg    => new Promise(resolve => … resolve(SendResult)) 
```

`myLongOperation`只论及 Kleisli 范畴，底层单子的类型与其无关。试试看:

[https://repl.it/@homami/Callback-5?lite=true](https://repl.it/@homami/Callback-5?lite=true)

看看我们如何为同一个`myLongOperation`函数提供一个 Kleisli 类别的承诺单子或一个 Kleisli 类别的回调单子。

我们最新的`myLongOperation`实现是最小的。这个实现描述了`myLongOperation`在没有任何额外噪声的情况下做了什么，它也符合我们的符号:`getUser ⋙ genMessage ⋙ sendMessage`。

## 总之

可组合性是任何回调地狱问题解决方案的本质。

我们实现了`Callback`类作为替代解决方案，我们发现我们的`Callback`类实际上与`Promises`有一些共同之处。它们都提供了一个`then()`。将它们绑定到返回新的`Promise`或`Callback`实例的函数。我们将这些结构命名为单子。

> 单子 x ≫= (x →单子 y) =单子 y
> 
> (单子 x)。那么(x = >单子 y) =单子 y

```
 Callback.pure(10).then(x => new Callback(cb => cb(null, x + 1)))

    Callback.resolve(10).then(x => new Promise(res => res(x + 1)) 
```

单子处理操作的结果。`Promise.resolve(10)`。会导致 10(包在承诺里)。

但是类别处理操作的输入和输出(我们将它们表示为 *Cat (x ↣ y)* )。`Func`是最简单的一类(对应正常函数)。

类别提供了一个类似于`Monad.then()`的`pipe()`功能。`then()`在其参数中接收一个函数，但相比之下`pipe()`接收另一个类别实例:

> 猫(x ↣ y) ⋙猫(y ↣ z) =猫(x ↣ z)
> 
> 猫(x ↣ y)。管道(猫(y ↣ z) ) =猫(x ↣ z)

```
 Func(x => x + 1).pipe(new Func(x => x * 3)).run(10) 
```

“返回单子的函数”形成一个范畴(称为 Kleisli 范畴)。

使用 Kleisli 类别，我们能够减少异步程序中的噪音和冗余。通常在函数式编程中，我们的目标是描述程序做什么，而不是处理程序如何工作。抽象(如类别或单子)会处理细节。

**演示链接:**

*   [回调单子用法](https://jsbin.com/wilune/edit?js,console)

*   [回调单子懒惰 vs 承诺](https://jsbin.com/jazadi/edit?js,console)

*   [回调单子作为回调地狱问题的解决方案](https://jsbin.com/wapeci/edit?js,console)

*   [函数组合不可交换](http://goo.gl/KU9jvr)

*   ( [id = x = > x)函数](https://ramdajs.com/repl/#?code%3Dconst%20plus1%20%20%3D%20x%20%3D%3E%20x%20%2B%201%0Aconst%20times2%20%3D%20x%20%3D%3E%20x%20%2A%202%0Aconst%20id%20%3D%20x%20%3D%3E%20x%0A%0Aconsole.log%28%60%28plus1%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7Bpipe%28plus1%2C%20times2%29%2810%29%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20plus1%29%2810%29%20%3D%20%24%7Bpipe%28times2%2C%20plus1%29%2810%29%7D%60%29%0A%0Aconsole.log%28%27------%27%29%0A%0Aconsole.log%28%60%28id%20%E2%8B%99%20times2%29%2810%29%20%3D%20%24%7Bpipe%28id%2C%20times2%29%2810%29%7D%60%29%0Aconsole.log%28%60%28times2%20%E2%8B%99%20id%29%2810%29%20%3D%20%24%7Bpipe%28times2%2C%20id%29%2810%29%7D%60%29%0A)

*   [功能类别](https://jsbin.com/jegidit/edit?js,console)

*   [管道功能的功能类别用法](https://jsbin.com/kecoxa/edit?js,console)

*   [Kleisli 许诺单子范畴](https://jsbin.com/hokimi/edit?js,console)

*   [Kleisli.id 函数](https://jsbin.com/wuduri/edit?js,console)

*   [回调和承诺单子的 Kleisli 类别用法演示](https://jsbin.com/miyaci/edit?js,console)

不管你是否喜欢这篇文章，或者如果我在文章的某个地方忘记了你，你可能想要检查一下[基本上足够的 FP 指南(在 javascript 中)](https://github.com/MostlyAdequate/mostly-adequate-guide)开源书。

虽然我们不需要使用任何库，但对我来说, [Ramda](http://ramdajs.com/) 是 JavaScript FP 库的旗手。**