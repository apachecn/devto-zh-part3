# JavaScript 中实现不变性的四种方式

> 原文：<https://dev.to/glebec/four-ways-to-immutability-in-javascript-3b3l>

## 摘要

本文介绍了在 JavaScript 中不变地更新数据结构的四种不同技术:

*   本机使用[扩展语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)和其他特性
*   Via 拉姆达的 [`lens`](https://ramdajs.com/docs/#lens) 抽象
*   Via Michel Weststrate 的 [`immer`](https://github.com/mweststrate/immer) 库
*   Via 脸书的 [`immutable`](https://facebook.github.io/immutable-js/) 图书馆

许多代码片段都是可运行和可编辑的，我们鼓励您尝试使用它们。

# 简介

函数式编程技术和设计模式在 JavaScript 应用程序中越来越受欢迎。比如 [RxJS](https://rxjs-dev.firebaseapp.com/) 、 [Redux](https://redux.js.org/) 、 [React](https://reactjs.org/) 等工具和框架都是从[函数式反应式编程](https://en.wikipedia.org/wiki/Functional_reactive_programming)中获取灵感。

在 FP 的众多不同特性中，[纯度](https://en.wikipedia.org/wiki/Pure_function)、[不变性](https://en.wikipedia.org/wiki/Immutable_object)和[持久数据结构](https://en.wikipedia.org/wiki/Persistent_data_structure)的相关方面产生了一些好处，如[引用透明性](https://en.wikipedia.org/wiki/Referential_transparency)和[幂等性](https://en.wikipedia.org/wiki/Idempotence)。简而言之，避免变异和副作用使得代码更容易推理和编写。

JavaScript 是一种[多范例](https://en.wikipedia.org/wiki/Comparison_of_multi-paradigm_programming_languages#cite_ref-44)编程语言。它允许使用函数式方法，但是没有一些专用函数式语言的便利性或保证。因此，如果 JS 开发人员想要更新数据而不发生突变，他们必须特别小心。例如，[Redux Redux reducer](https://redux.js.org/basics/reducers)“必须**永不**改变它的参数，执行副作用…，[或]调用非纯函数”，但是 Redux 本身并没有提供遵守这些限制的工具。

本文不会关注不变性的好处，而是探索如何在 JS 中实现不变性。

# 什么是不可变更新

考虑一个增加对象的`.counter`属性的函数:

`const obj1 = { bools: [true, false], counter: 7 } const obj2 = { bools: [false, true], counter: 3 } function incrementObjCounter (obj) { // naive solution const newObj = { bools: obj.bools, // same reference counter: obj.counter + 1 // updated data } return newObj } const newObj1 = incrementObjCounter(obj1) const newObj2 = incrementObjCounter(obj2) console.log(obj1, newObj1) // different objects console.log(obj2, newObj2) // different objects console.log(obj1.bools === newObj1.bools) // shared reference console.log(obj2.bools === newObj2.bools) // shared reference`

我们不是修改原始对象，而是用更改的数据生成一个新对象。注意，一个更简单的解决方案可能会递归地复制原始对象中的所有数据。这通常被称为[深度克隆](https://lodash.com/docs/4.17.11#cloneDeep)，并且使用双倍的内存量。

但是，如果我们不想改变基本对象中的数据，它可以安全地与新对象共享，如上面的`.bools`属性所示。这种*结构共享*意味着不可变更新通常可以重用现有内存。

不幸的是，上面的代码很脆弱；它使用硬编码的属性名，即使数据没有被更新。该函数既不可重复用于其他对象形状，也不容易维护。

# 不可变更新的困难

假设我们有如下嵌套数据:

```
const dino = {
  name: 'Denver',
  type: 'dinosaur',
  friends: [
    {
      name: 'Wally',
      type: 'human',
      pets: [
        {
          name: 'Rocky',
          type: 'dog'
        }
      ]
    },
    {
      name: 'Casey',
      type: 'human'
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

也许我们可以给沃利一只新的宠物兔子，形状是`{ name: 'Ears', type: 'rabbit' }`。试一试；`dino`值在以下可编辑代码块的范围内。与大多数现实世界的应用程序不同，我们有深度冻结的`dino`，所以你会知道如果你不小心试图改变它——将会抛出一个错误。

`const registerTests = require('@runkit/glebec/tape-dispenser/releases/1.0.0') const test = (actual) => registerTests(t => { t.deepEqual( actual, { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' }, { name: 'Ears', type: 'rabbit' } ] }, { name: 'Casey', type: 'human' } ] } ) t.end() }) function deepFreeze(object) { var propNames = Object.getOwnPropertyNames(object); for (let name of propNames) { let value = object[name]; object[name] = value; if ((typeof value) === 'object') { object[name] = deepFreeze(value) } } return Object.freeze(object); } const dino = deepFreeze({ name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] })` `await (function runTestInStrictMode () { 'use strict'; // using functional strict mode because of runkit limitations // dino is in scope const updatedDino = undefined; // give Wally a new pet rabbit! return test(updatedDino); })()`

你是否设法正确地生成了一只新恐龙，而没有抛出任何与突变相关的错误？在一个实际的代码库中，你有多少信心可以避免这些错误？请记住，通常情况下，像这样的物体不会被深度冻结——它们会默默地接受突变。

在这个挑战中，我们让你添加一个物体。更新一个深度嵌套的对象或者删除一个对象呢？请随意尝试下面的内容，然后继续阅读一些评论。

`function deepFreeze(object) { var propNames = Object.getOwnPropertyNames(object); for (let name of propNames) { let value = object[name]; object[name] = value; if ((typeof value) === 'object') { object[name] = deepFreeze(value) } } return Object.freeze(object); } const dino = deepFreeze({ name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] })` `(function runTestInStrictMode () { 'use strict'; // using functional strict mode because of runkit limitations // dino is in scope const dinoRenamedRocky = undefined // change dino.friends[0].pets[0].name const dinoRemovedRocky = undefined // remove dino.friends[0].pets[0] console.log(dino, dinoRenamedRocky, dinoRemovedRocky) // three different values })()`

# 对香草 JS 行为的回顾

## 原语是不可变的(但也是可重新赋值的)

默认情况下，JavaScript 原语(未定义、空、布尔、数字、字符串和符号值)是不可变的，因此您已经被禁止永久更改原语值本身。

`const str = 'hello' str[0] = 'j' // silently ignores mutation attempt console.log(str) // "hello", not "jello"`

当然，JavaScript 确实允许共享的**变量**的*重新赋值*，这与突变不是一回事，但也存在许多相同的问题。

`let x = 5 badInc = () => { x = x + 1; return x } badDbl = () => { x = x * 2; return x } console.log(badInc(x) + badDbl(x)) // neither commutative nor idempotent`

尝试将上述表达式中的术语转换为`badDbl(x) + badInc(x)`并重新运行代码。由于重新绑定共享的`x`变量所固有的有状态性，我们得到了不同的结果，违背了我们对加法如何工作的预期。

## 对象是可变的(也是可冻结的)

任何不属于上面列出的基本类型的东西都被认为是 JS 中的一个*对象*——包括数组和函数。初学者对 JavaScript 的一个常见误解是`const`声明会阻止变异。不是这样；`const`仅阻止*重新分配*。

`const obj = {} const arr = [] const fnc = () => {} obj.mutated = true arr.mutated = true fnc.mutated = true console.log(obj.mutated, arr.mutated, fnc.mutated) // true x 3`

JavaScript 确实有静态方法`Object.freeze(obj)`来防止重新分配属性。在[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)下，试图在一个冻结的对象上设置属性会抛出一个有用的错误。遗憾的是，在非严格模式下，这种尝试只会被无声地拒绝。(注意`freeze`本身变异了对象。)

`const obj = { example: 9000 } obj.example++ // mutates obj Object.freeze(obj) // mutates obj by freezing it obj.example++ // silently ignored in non-strict mode (error in strict mode) console.log(obj.example) // 9001, not 9002`

但是，请注意-`freeze`只防止重新分配顶级属性，并不防止那些属性中引用的对象发生变化。为此，你需要一个非本地的"[深度冻结](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#What_is_shallow_freeze)"算法来递归冻结对象。

`const obj = { o: { x: 50 } } Object.freeze(obj) obj.o.x++ // still allowed console.log(obj) // inner reference mutated, oops`

至少在严格模式下，冻结对象可以通过早期失败(在尝试突变时)而不是晚期失败(当意外状态产生不正确的行为时)来帮助发现和调试错误。然而实际上，冻结并不常见，可能是因为:

*   作者需要预测一个给定的物体值得额外的冷冻努力，违背了 YAGNI 原理
*   深度冻结算法与深度克隆算法一样，在 JS 中有边缘情况和陷阱(例如，循环、正确检测对象)
*   递归冻结深度嵌套的对象可能是一个性能问题(不管有效与否)

# 方法 1:本机不可变更新

那么，在希望避免变异时，JS 提供了哪些工具呢？由于原语已经是不可变的，函数很少被用作可变的数据容器，实际上问题是如何生成新版本的`Object`和`Array` s。

## [传播语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

截至 ES2015，spread `...`运算符允许将[可迭代](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)值扩展为显式参数。当在新数组或对象文字中使用时，这允许将现有值的属性复制到新值中:

`const obj1 = { type: 'data' } const arr1 = [1, 2, 3] const obj2 = { ...obj1, subtype: 'stuff' } // adds a key-val pair const arr2 = [ ...arr1, 'cheese' ] // adds an element // distinct old and new versions console.log(obj1, obj2) console.log(arr1, arr2)`

特别是对于对象，这使得顶级键的*更新*变得容易:

`const obj1 = { type: 'data', age: 55 } const obj2 = { ...obj1, age: obj1.age + 1 } // modified age console.log(obj1, obj2)`

在某些环境中，可能不支持对象扩展语法，但 [`Object.assign`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) (还有 ES2015)可能可用。

## 原生方法的问题

Spread 使向数组添加新值、向对象添加新的键值对或修改对象上的键值对变得非常方便。然而，删除键值对并不简单。使用[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)有一些技巧，但是在处理嵌套对象时不方便。

对于数组，可以通过`filter` ing 或`slice` ing 来删除特定元素:

`const arr = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'] const newArrA = [...arr.slice(0, 2), ...arr.slice(3)] const newArrB = arr.filter(color => color !== 'yellow') console.log(arr, newArrA, newArrB)`

更新数组元素可以通过`slice`或`map`完成:

`const arr = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'] const newArrA = [...arr.slice(0, 2), 'sunshine', ...arr.slice(3)] const newArrB = arr.map(color => color === 'yellow' ? 'sunshine' : color) console.log(arr, newArrA, newArrB)`

这些方法是可行的，但是有些麻烦并且容易出错。而我们只是在处理浅薄的数据；筑巢变得更加困难。让我们回到这篇文章中最初的例子，在丹佛的朋友 Wally 的宠物列表中添加一只兔子。下面是一个使用普通 JS 的可能解决方案:

`const dino = { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] }` `const updatedDino = { ...dino, friends: [ { ...dino.friends[0], pets: [...dino.friends[0].pets, { name: 'Ears', type: 'rabbit' }] }, ...dino.friends.slice(1) ] } console.log(dino, updatedDino)`

这是可行的，但也有一些明显的缺点:

*   它非常复杂和冗长，难以阅读、编写和维护
    *   数组和对象分布之间的频繁交替很容易让人忘记发生了什么
    *   越深入对象，引用链增长的可能性就越高(例如`pets: [...dino.friends[0].pets ]`)
    *   修改数组中的一个特定值需要添加`map`或`slice`到组合中，增加了噪声
    *   任何执行上的错误都会是实践中无声的突变错误，除非`dino`被深度冷冻**和**你处于严格模式
*   表现不理想
    *   扩展语法调用迭代协议
    *   创建已更改对象/数组(例如`pets`和`friends`数组)的副本是`O(n)`时间复杂度

# 方法二:[拉姆达`lens`](https://ramdajs.com/docs/#lens)

## 上下文

[透镜](https://github.com/ekmett/lens/wiki/History-of-Lenses)是一种与源自 [Haskell](https://www.haskell.org/) 的嵌套数据交互的方法，Haskell 是一种懒惰的(因此必然是纯粹的)函数式语言，具有 [Hindley-Milner](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system) 推断的静态类型。在这种语言中，它们是非常灵活和通用的工具，可以无缝地处理各种数据类型。

镜头和相关工具是一个很深的主题，在 JavaScript 中我们没有类型限制，这使得镜头在 Haskell 中特别有价值。然而，核心思想足够简单和有用，以至于它出现在一个流行的功能性 JS 实用程序库 [Ramda](https://ramdajs.com/) 中。

## 高层

从概念上讲，镜头是一对功能:

*   “焦点”元素`a`的*获取器*，该元素可以从一些“源”数据`s`中导出
*   一个*设置器*，它可以更新`a`，不断生成一个新的`s`

就实现而言，镜头实际上是一个*单一功能*，根据其使用方式执行上述任一职责。给定的镜头功能不是在源上直接调用的；相反，像`view`、`set`和`over`这样的辅助函数负责正确地使用镜头。

```
const { view } = require('ramda')
const extractedData = view(someLens, sourceData) // NOT someLens(wrappingData) 
```

Enter fullscreen mode Exit fullscreen mode

因为镜头是单独的功能，所以可以直接[组合](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)在一起形成新的镜头(比如[组合子模式](https://wiki.haskell.org/Combinator_pattern))。

```
const { compose } = require('ramda')
const deepLens = compose(outerLens, innerLens) 
```

Enter fullscreen mode Exit fullscreen mode

## 演示

最常见的是，使用 Ramda，人们不会从头开始编写镜头，而是使用类似 [`lensIndex`](https://ramdajs.com/docs/#lensIndex) 和 [`lensProp`](https://ramdajs.com/docs/#lensProp) 的方法来生成正确的镜头。

`const dino = { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] }` `const R = require('ramda') // dino is in scope // making a lens for the "friends" property const friendsLens = R.lensProp('friends') // viewing data using a lens const friends = R.view(friendsLens, dino) console.log(friends) // immutably setting data using a lens const lonelyDino = R.set(friendsLens, [], dino) // aww, no more friends console.log(lonelyDino) // immutably mapping data using a lens const addEveryone = arr => [...arr, 'everyone'] const popularDino = R.over(friendsLens, addEveryone, dino) console.log(popularDino) console.log(dino) // unchanged`

`set`和`over`方法使用期望的更新生成新数据，但是像对象/数组分布一样，新值也与旧值共享任何未更新的引用。

所有这些都很好，但是嵌套数据呢？为此，标准的从右到左功能组合可以将多个镜头缝合在一起。

`const dino = { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] }` `const R = require('ramda') // dino is in scope // making some lenses const friendsLens = R.lensProp('friends') const lens0 = R.lensIndex(0) const petsLens = R.lensProp('pets') const nameLens = R.lensProp('name') // stacking lenses together into a single lens const frns0pets0name = R.compose( friendsLens, // outermost lens first lens0, petsLens, lens0, nameLens // innermost lens last ) // viewing data using a lens console.log(R.view(frns0pets0name, dino)) // immutably setting data using a lens console.log(R.set(frns0pets0name, 'Spot', dino)) // immutably mapping data using a lens console.log(R.over(frns0pets0name, (s => s + '!'), dino)) console.log(dino) // unchanged`

与我们的对象/数组展开+映射技术相比，上面的方法要简洁、声明性强、简单且简单。但是它变得更加容易；将镜头组合在一起聚焦在一个嵌套的属性链上是很常见的，所以 Ramda 提供了一个方便的 [`lensPath`](https://ramdajs.com/docs/#lensPath) 函数。

`const dino = { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] }` `const R = require('ramda') // dino is in scope const frns0pets0name = R.lensPath(['friends', 0, 'pets', 0, 'name']) console.log(R.set(frns0pets0name, 'Spot', dino)) console.log(dino) // unchanged`

当然，你也可以将这个镜头与`view`和`over`一起使用——自己试试。最终，我们的最终解决方案被浓缩为两行易读的代码——一行从路径构建透镜，另一行使用透镜在一些数据中不变地设置深度嵌套的属性。

## 你需要的只是镜头？

镜头是具有多种能力(读取/更新/设置)的普通可组合功能，是具有所有附带技巧的高功能解决方案，这些技巧意味着:部分应用、高阶使用、一流便携性、动态行为等。然而，虽然 Ramda 的 lens 函数使得查看、设置和映射嵌套属性变得很容易，但是删除就有点间接了；必须使用普通 JS 移除技术来映射父道具:

`const R = require('ramda') const obj = { items: ['a', 'b'] } const removeFirst = arr => arr.slice(1) const updated = R.over(R.lensProp('items'), removeFirst, obj)`

通过像 Ramda 这样的库，镜头的基本实例化和使用可能足够简单，但是用户应该知道镜头和其他“光学器件”*有一些高级用途和重要的问题。例如，违反[透镜定律](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/a-little-lens-starter-tutorial#the-lens-laws-)的类透镜函数会导致奇怪的边缘情况。

*( *透镜的子集是同构的，这使你能够对数据结构进行操作，就好像它们是其他(等价的)数据结构一样。还有棱镜和遍历，棱镜支持与可能是或可能不是某种形状的数据进行交互，遍历可以一次关注多个子部分。*)

# 接近 3: [沉浸](https://github.com/mweststrate/immer)

一些开发人员可能更喜欢用一种更惯用的方法来处理 JS 中的不可变更新，这种方法不需要导入更大的工具套件或学习新的 API。迈克尔·韦斯特拉特的图书馆 [Immer](https://github.com/mweststrate/immer) 就是为此而专门建造的。

Immer 的聪明之处在于以一种[元编程](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Meta_programming)的形式，改变普通变异动作*的含义*——拦截赋值(`=`)、不安全方法(`push`)和其他情况。具体来说，它使用 [`Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 构造函数来创建原始对象的“草稿”版本。您可以随意修改这个草稿，甚至直接修改嵌套很深的引用。Immer 没有允许这些更改实际发生，而是将它们转换成对原始对象的不可变更新。

## 演示

Immer 的核心函数`produce`，有如下签名:

```
produce(currentState, producer: (draftState) => void): nextState 
```

Enter fullscreen mode Exit fullscreen mode`const { produce } = require('immer') const original = { color: 'blue', items: [5, 6], thing: { x: true }, shared: {} } const updated = produce(original, (draft => { draft.color = 'red' draft.items.push(917) delete draft.thing.x draft.thing.y = false })) console.log(original, updated) // no mutation console.log(original.shared === updated.shared) // no naive copying`

正如上面最后的`console.log`所示，Immer 正在使用结构共享，就像我们的手动扩展和 Ramda 镜头解决方案一样。优点包括:

*   新属性在开发模式下会自动冻结
*   `produce`有适用于部分应用的修订版
*   `immer`是一个小型的专用库
*   不需要学习新的 API(除了一个函数包装器)
*   大多数情况下不需要特别小心，认知开销更少

## 你需要的只是 Immer？

虽然 Immer 非常灵活，但它最终还是做了与我们以前的解决方案相同的`Object.assign` / spread / map 类型的操作。因此，虽然 Immer 受益于结构共享，但一些大数据的更新可能会很慢(例如，`O(n)`复制数组)。另外，[默认情况下 Immer 只作用于普通数组和对象](https://github.com/mweststrate/immer#supported-object-types)(的某些属性)，而不是其他内置的，比如`Map`和`Set`。对于其他陷阱，[查阅文档](https://github.com/mweststrate/immer#pitfalls)。

# 方法四:[不可变. js](http://facebook.github.io/immutable-js/)

到目前为止，我们研究的三种方法都关注本质上相同的数据结构——嵌套的`Object`和`Array`。本地方法、透镜和`immer`都“与其他方法配合得很好”,因为其他库和代码库通常会提供和期望这样的数据类型。

然而，这些结构的一个缺点是，它们在设计时没有考虑到不可变的更新。要向数组中添加一个项目，您必须创建一个包含所有其他元素的数组副本——一个`O(n)`操作。如果您没有特别注意，您可以调用像`sort`这样的本机方法，而不会意识到它改变了原始数据结构。

另一种方法是切换到[纯功能数据结构](http://www.cs.cmu.edu/~rwh/theses/okasaki.pdf) (PDF 链接)，或者至少是具有高效不可变更新操作的数据结构。这样的数据结构将暴露一个只包含*和*纯操作的 API，减少出错的机会。

脸书的库[不可变](http://facebook.github.io/immutable-js/)利用了这样的结构。虽然它们是用普通的 JavaScript 实现的，但细节对用户是隐藏的，用户现在与`List`和`Map`等类交互(不同于同名的 ES2015 类)。对于大量的数据，现在修改这些实体要快得多；比如，`Map`的 set 和 get 函数有`O(log32 n)`的时间复杂度，`List`的`push`和`pop`方法尽管返回新的更新列表却是`O(1)`。

## 演示

不可变是一个有多个类的大型库，每个类都有许多方法。我们将演示这个库的一小部分，从将一些典型的 JS 数据转换成一系列嵌套的`Map`和`List`开始。

`const dino = { name: 'Denver', type: 'dinosaur', friends: [ { name: 'Wally', type: 'human', pets: [ { name: 'Rocky', type: 'dog' } ] }, { name: 'Casey', type: 'human' } ] }` `const Immutable = require('immutable') // dino is in scope const immDino = Immutable.fromJS(dino) // view nested (perhaps missing!) data const dogName = immDino.getIn(['friends', 0, 'pets', 0, 'name']) console.log(dogName) // change nested data (immutably), perhaps at a brand new path const immDino2 = immDino.setIn(['friends', 0, 'pets', 0, 'name'], 'Spot') console.log(immDino2)`

有些优点没有直接展示出来，但值得指出:

*   如果一个属性不存在，`getIn`将短路并返回`undefined`，而不是抛出一个`cannot read x of undefined`错误
*   如果一个属性不存在，`setIn`将会生成新的`Map`对象
*   我们可以用 Immutable.js `Map` s 和`List` s 做更多的事情，比如`.map`、`.push`、`.sort`等。–全部以纯净、非破坏性的方式进行

最后，如果其他一些库或框架需要一个普通的 JS 对象或数组，我们可以使用`.toJS()`将其转换回来。

## 你需要的都是不可变的？

性能和安全是倾向于使用`immutable`的主要原因，但是总是有权衡要考虑。

*   不可变要求开发人员学习一个新的、相对较大的 API
*   调试`immutable`值比普通对象和数组更不符合人机工程学
*   使用不可变类的项目可能需要与更简单的数据类型相互转换，这可能会抵消性能优势

# 我想要这一切

正如编程中经常遇到的情况一样，所研究的每种方法都有优点和缺点。谢天谢地，它们并不相互排斥。例如，Brian Lonsdorf 在他的文章 [Lenses with Immutable.js](https://medium.com/@drboolean/lenses-with-immutable-js-9bda85674780) 中演示了使用 Ramda lenses 将本机对象/数组与 Immutable.js 映射/列表混合。类似地，可以在传递给 Ramda `over`的函数中，或者在调用 Immer 的`produce`函数中使用数组展开。了解多种方法可以让你达到适合你的用例的抽象。

| 工具 | 学习曲线 | 安全 | 表演 | 概念 | TL；速度三角形定位法(dead reckoning) |
| --- | --- | --- | --- | --- | --- |
| [原生](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) | JS 开发人员希望了解它，但需要时间来掌握 | 不良，常见的错误来源 | 对于大量数据，速度较慢(线性) | 手动复制嵌套属性，`map` / `filter` / `slice`进行更新或删除 | 内置的 |
| [拉姆达(镜头)](https://ramdajs.com/docs/) [(52 kB)](https://bundlephobia.com/result?p=ramda@0.26.1) | 少量的概念，但对 FP 新手来说可能是陌生的 | 很好，但是小心路径 | 与本地相当 | 为嵌套数据创建透镜，使用它们和`view` / `set` / `over`，通过 FP 技术操作(合成，部分应用) | 多功能图案 |
| [Immer](https://github.com/mweststrate/immer) [(13 kB)](https://bundlephobia.com/result?p=immer@2.0.0) | 一个 API 方法 | 很好，但是有一些边缘情况 | 与本地相当 | 在草稿对象上使用正常变化的本机动作，动作反而变成不可变的更新 | 简易 API |
| [不可变](http://facebook.github.io/immutable-js/) [(63 kB)](https://bundlephobia.com/result?p=immutable@4.0.0-rc.12) | 具有许多 API 方法的大型库 | 很好，但是小心路径 | 本质上很好，但是由于转换而变慢了 | 使用库的数据结构和方法，而不是本机类型，必要时进行转换 | 更快的算法 |

* * *

## 附录:通过函数编码的不变性

Dierk knig 教授提出了不可变数据的另一种技术:将数据存储为函数。

> ![Dierk König profile image](img/f2a1cdf42701929de655ed084ebb2669.png)Dierk knig@ mittie![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ g _ lebec](https://twitter.com/g_lebec)一如既往的伟大！我倾向于使用 Vireo 及其泛化 Tuple(n)来实现不变性。类似于求和类型。此外，我避免任何依赖。2019 年 2 月 13 日上午 09:36[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1095617645767020544)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1095617645767020544)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1095617645767020544)

在 JavaScript 中， *Vireo* 组合子(在 Raymond Smullyan 的著名著作《模仿一只知更鸟的 T2》中命名)的定义如下:

```
const vireo = a => b => f => f(a)(b) 
```

Enter fullscreen mode Exit fullscreen mode

当`vireo`被应用两次(两个独立的数据段)时，它返回一个在两个数据点上闭合的最终函数。结果在概念上是一对数字，*存储为函数闭包*。为了访问存储的数字，提供了最后一个函数参数:

`const vireo = a => b => f => f(a)(b)` `const pairOfNums = vireo(5)(8) // f => f(5)(8) pairOfNums(a => b => console.log(a)) // logs 5 pairOfNums(a => b => console.log(b)) // logs 8`

注意，在这个例子中，一旦生成了值对，就没有办法实际修改这些值——闭包充当一种特权数据的形式。怎样才能“更新”这样的一对？我们根据旧函数创建了一个新函数:

`const vireo = a => b => f => f(a)(b)` `const pairOfNums = vireo(5)(8) // f => f(5)(8) const fst = a => _ => a const snd = _ => b => b const newPairWithSecondNumDoubled = f => { const firstNum = pairOfNums(fst) const secondNum = pairOfNums(snd) return f(firstNum)(secondNum * 2) } console.log( newPairWithSecondNumDoubled(fst), // 5 newPairWithSecondNumDoubled(snd) // 16 )`

为了使这更容易，我们可以编写助手函数来获取、设置和映射…

`const vireo = a => b => f => f(a)(b)` `const getL = p => p(l => _ => l) const getR = p => p(_ => r => r) const getLR = p => p(l => r => [l, r]) const setL = newL => p => vireo(newL)(getR(p)) const setR = newR => p => vireo(getL(p))(newR) const mapL = transform => p => vireo(transform(getL(p)))(getR(p)) const mapR = transform => p => vireo(getL(p))(transform(getR(p))) const numPair = vireo(3)(7) const show = pair => console.log(getLR(pair)) show(numPair) // [3, 7] show(setL(0)(numPair)) // [0, 7] show(mapR(x => x * 2)(numPair)) // [3, 14]`

通过闭包存储数据，并创建遵从旧函数的新函数，可以扩展到更复杂的数据，并且在 JS 中也变得更习惯；然而，这对于它自己的文章来说是一个足够大的主题。如果这种方法引起了你的兴趣，Sandy Maquire 的关于类型的[思维展示了一种聪明的方法(在 Haskell 中)将](https://leanpub.com/thinking-with-types)[井字游戏](https://en.wikipedia.org/wiki/Tic-tac-toe)板编码为从坐标到数据的函数。下面用一个 JS 版本做实验！

`const emptyBoard = (x, y) => null const setPos = (atX, atY, newVal, oldBoard) => { const oldVal = oldBoard(atX, atY) const newBoard = (x, y) => { return (x === atX && y === atY) ? newVal : oldBoard(x, y) } return newBoard } const printBoard = (board, limitX, limitY) => { for (let x = 0; x < limitX; x++) { for (let y = 0; y < limitY; y++) { console.log('pos ' + x + ', ' + y + ' is ' + board(x, y)) } } } const b2 = setPos(0, 0, 'x', emptyBoard) const b3 = setPos(1, 1, 'o', b2) printBoard(b3, 2, 2) // NB: if you log a larger board, runkit paginates the results!`